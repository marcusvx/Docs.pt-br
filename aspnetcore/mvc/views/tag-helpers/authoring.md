﻿---
title: "Criação de auxiliares de marcação no ASP.NET Core"
author: rick-anderson
description: "Saiba como criar auxiliares de marcação no ASP.NET Core"
manager: wpickett
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/19/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 040c26bfccb8f258b0941bed4bc936cf7a16324a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2018
---
# <a name="author-tag-helpers-in-aspnet-core-a-walkthrough-with-samples"></a>Criação de auxiliares de marcação no ASP.NET Core, um passo a passo com exemplos

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([como baixar](xref:tutorials/index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Introdução aos Auxiliares de Marca

Este tutorial fornece uma introdução à programação de auxiliares de marcação. [Introdução aos auxiliares de marcação](intro.md) descreve os benefícios que os auxiliares de marcação fornecem.

Um auxiliar de marca é qualquer classe que implementa a interface `ITagHelper`. No entanto, quando cria um auxiliar de marca, você geralmente deriva de `TagHelper`, o que fornece acesso ao método `Process`.

1. Crie um novo projeto do ASP.NET Core chamado **AuthoringTagHelpers**. Você não precisará de autenticação para esse projeto.

2. Criar uma pasta para armazenar os auxiliares de marca chamados *TagHelpers*. A pasta *TagHelpers* pasta *não* é necessária, mas é uma convenção comum. Agora vamos começar a escrever alguns auxiliares de marca simples.

## <a name="a-minimal-tag-helper"></a>Um auxiliar de marca mínimo

Nesta seção, você escreve um auxiliar de marca que atualiza uma marca de email. Por exemplo:

```html
<email>Support</email>
   ```

O servidor usará nosso auxiliar de marcação de email para converter essa marcação no seguinte:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

Ou seja, uma marca de âncora que torna isso um link de email. Talvez você deseje fazer isso se estiver escrevendo um mecanismo de blog e precisar que ele envie emails para o marketing, suporte e outros contatos, todos para o mesmo domínio.

1.  Adicione a classe `EmailTagHelper` a seguir à pasta *TagHelpers*.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]
    
    **Observações:**
    
    * Auxiliares de marcação usam uma convenção de nomenclatura que tem como alvo os elementos do nome da classe raiz (menos o *TagHelper* parte do nome de classe). Neste exemplo, o nome da raiz **Email**TagHelper é *email*, portanto, a marca `<email>` será direcionada. Essa convenção de nomenclatura deve funcionar para a maioria dos auxiliares de marcação, posteriormente, mostrarei como substituí-la.
    
    * O `EmailTagHelper` classe deriva de `TagHelper`. O `TagHelper` classe fornece métodos e propriedades para gravar tag helpers.
    
    * O método 'Process' substituído controla o que o auxiliar de marca faz quando é executado. A classe 'TagHelper' também fornece uma versão assíncrona ('ProcessAsync') com os mesmos parâmetros.
    
    * O parâmetro de contexto para `Process` (e `ProcessAsync`) contém informações associadas à execução da marca HTML atual.
    
    * O parâmetro de saída para `Process` (e `ProcessAsync`) contém um elemento HTML com estado que representa a fonte original usada para gerar uma marca HTML e o conteúdo.
    
    * Nosso nome de classe tem um sufixo **TagHelper**, que *não* é necessário, mas que é considerado uma convenção de melhor prática. Você pode declarar a classe como:
    
    ```csharp
    public class Email : TagHelper
    ```

2.  Para disponibilizar a classe `EmailTagHelper` para todas as nossas exibições do Razor, adicione a diretiva `addTagHelper` ao arquivo *Views/_ViewImports.cshtml*: [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]
    
    O código anterior usa a sintaxe de curinga para especificar que todos os tag helpers no nosso assembly estarão disponíveis. A primeira cadeia de caracteres após `@addTagHelper` Especifica o tag helper para carregar (Use "*" para todos os tag helpers), e a segunda cadeia de caracteres "AuthoringTagHelpers" Especifica o assembly de tag helper está em. Além disso, observe que a segunda linha coloca nos tag helpers do MVC do ASP.NET Core usando a sintaxe de curinga (os auxiliares são discutidos em [Introdução ao tag helpers](intro.md).) É o `@addTagHelper` diretiva que disponibiliza o tag helper para o modo de exibição do Razor. Como alternativa, você pode fornecer o nome totalmente qualificado (FQN) de um tag helper, conforme mostrado abaixo:
    
```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```
    
<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->
    
Para adicionar um tag helper para uma exibição usando um FQN, primeiro adicione o FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) e, em seguida, o nome do assembly (*AuthoringTagHelpers*). A maioria dos desenvolvedores vão preferir usar a sintaxe de curinga. [Introdução ao tag helpers](intro.md) apresenta detalhes sobre a sintaxe de adição, remoção, hierarquia e curinga do tag helper.
    
3.  Atualize a marcação no arquivo *Views/Home/Contact.cshtml* com essas alterações:

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

4.  Execute o aplicativo e use seu navegador favorito para exibir o código-fonte HTML para verificar se as marcas de email são substituídas pela marcação de âncora (por exemplo, `<a>Support</a>`). *Suporte* e *Marketing* são renderizados como links, mas não têm um atributo `href` para torná-los funcionais. Corrigiremos isso na próxima seção.

Observação: assim como atributos e marcas HTML, as marcações, os nomes de classes e os atributos no Razor e no C# não diferenciam maiúsculas de minúsculas.

## <a name="setattribute-and-setcontent"></a>SetAttribute e SetContent

Nesta seção, atualizaremos o `EmailTagHelper` para que ele crie uma marca de âncora válida para email. Vamos atualizá-lo para obter informações de uma exibição do Razor (na forma de um atributo `mail-to`) e usar isso na geração da âncora.

Atualize a classe `EmailTagHelper` com o seguinte:

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

**Observações:**

* Nomes de classe e de propriedade na formatação Pascal Case para auxiliares de marca são convertidos em seu [kebab case em minúsculas]](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Portanto, para usar o atributo 'MailTo', você usará o equivalente de '<email mail-to="value"/>'.

* A última linha define o conteúdo concluído para nosso tag helper minimamente funcional.

* A linha realçada mostra a sintaxe para adicionar atributos:

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Essa abordagem funciona para o atributo "href" como no momento, ele não existe na coleção de atributos. Você também pode usar o `output.Attributes.Add` para adicionar um atributo do tag helper ao final da coleção de atributos de marca.

1.  Atualize a marcação no arquivo *Views/Home/Contact.cshtml* com essas alterações: [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

2.  Execute o aplicativo e verifique se ele gera os links corretos.
    
    > [!NOTE]
    >Se você pretende escrever o autofechamento da marca de email (`<email mail-to="Rick" />`), a saída final também é o autofechamento. Para permitir a capacidade de gravar a marca com uma marca de início (`<email mail-to="Rick">`), é necessário decorar a classe com o seguinte:
    >
    > [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]
    
    Com um auxiliar de marca da marca de email com autofechamento, a saída será '<a href="mailto:Rick@contoso.com" />'. As marcas de âncora com autofechamento são um HTML inválido. Portanto, não é recomendável criá-las, mas talvez criar um auxiliar de marca com autofechamento. Auxiliares de marca definem o tipo da propriedade 'TagMode' após a leitura de uma marca.
    
### <a name="processasync"></a>ProcessAsync

Nesta seção, escreveremos um auxiliar de email assíncrono.

1.  Substitua a classe `EmailTagHelper` pelo seguinte código:

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

    **Observações:**

    * Essa versão usa o método `ProcessAsync` assíncrono. O `GetChildContentAsync` assíncrono retorna uma `Task` que contém o `TagHelperContent`.

    * Use o parâmetro `output` para obter o conteúdo do elemento HTML.

2.  Faça a alteração a seguir no arquivo Views/Home/Contact.cshtml para que o auxiliar de marca possa obter o email de destino.

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

3.  Execute o aplicativo e verifique se ele gera links de email válidos.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent e PostContent.SetHtmlContent

1.  Adicione a classe `BoldTagHelper` a seguir à pasta *TagHelpers*.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

    **Observações:**
    
    * O atributo `[HtmlTargetElement]` passa um parâmetro de atributo que especifica que qualquer elemento HTML que contenha um atributo HTML nomeado "bold" terá uma correspondência e que o método de substituição `Process` na classe será executado. Em nossa amostra, o método `Process` remove o atributo "bold" e envolve a marcação contida com `<strong></strong>`.
    
    * Como você não deseja substituir a conteúdo de marca existente, você precisa escrever a marca `<strong>` de abertura com o método `PreContent.SetHtmlContent` e a marca `</strong>` de fechamento com o método `PostContent.SetHtmlContent`.
    
2.  Modifique a exibição *About.cshtml* para que ela contenha um valor de atributo `bold`. O código completo é mostrado abaixo.

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

3.  Execute o aplicativo. Use seu navegador favorito para inspecionar a origem e verificar a marcação.

    O `[HtmlTargetElement]` atributo acima se destina somente a marcação HTML que fornece um nome de atributo de "bold". O `<bold>` elemento não foi modificado pelo tag helper.

4. Comente a linha de atributo `[HtmlTargetElement]` e ela usará como padrão as marcações `<bold>` de direcionamento, ou seja, a marcação HTML do formato `<bold>`. Lembre-se de que a convenção de nomenclatura padrão fará a correspondência do nome da classe **Bold**TagHelper com as marcações `<bold>`.

5. Execute o aplicativo e verifique se a marca '<bold>' é processada pelo auxiliar de marca.

A decoração de uma classe com vários atributos `[HtmlTargetElement]` resulta em um OR lógico dos destinos. Por exemplo, o uso do código a seguir, uma marca de negrito ou um atributo de negrito terá uma correspondência.

[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Quando vários atributos são adicionados à mesma instrução, o tempo de execução trata-os como um AND lógico. Por exemplo, no código abaixo, um elemento HTML precisa ser nomeado "bold" com um atributo nomeado "bold" (`<bold bold />`) para que haja a correspondência.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Também use o `[HtmlTargetElement]` para alterar o nome do elemento de destino. Por exemplo, se você deseja que o `BoldTagHelper` seja direcionado a marcações `<MyBold>`, use o seguinte atributo:

```csharp
[HtmlTargetElement("MyBold")]
   ```

## <a name="pass-a-model-to-a-tag-helper"></a>Passe um model para um tag helper

1.  Adicionar uma pasta *models*.

2.  Adicione a seguinte classe `WebsiteContext` à pasta *Models*:

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

3.  Adicione a classe `WebsiteInformationTagHelper` a seguir à pasta *TagHelpers*.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]
    
    **Observações:**
    
    * Conforme mencionado anteriormente, os auxiliares de marca convertem nomes de classes e propriedades dos auxiliares de marca do C# na formatação Pascal Case em [kebab case em minúsculas](http://wiki.c2.com/?KebabCase). Portanto, para usar o 'WebsiteInformationTagHelper' no Razor, você escreverá '<website-information />'.
    
    * Você não identifica de forma explícita o elemento de destino com o atributo `[HtmlTargetElement]`. Portanto, o padrão de `website-information` será o destino. Se você aplicou o seguinte atributo (observe que não está em kebab case, mas corresponde ao nome da classe):
    
    ```csharp
    [HtmlTargetElement("WebsiteInformation")]
    ```
    
    A marca `<website-information />` em kebab case em minúsculas não terá uma correspondência. Caso deseje usar o atributo `[HtmlTargetElement]`, use o kebab case, conforme mostrado abaixo:
    
    ```csharp
    [HtmlTargetElement("Website-Information")]
    ```
    
    * Elementos de fechamento automático não têm nenhum conteúdo. Neste exemplo, a marcação Razor usará uma marca de fechamento automático, mas o tag helper criará um [seção](http://www.w3.org/TR/html5/sections.html#the-section-element) elemento (que não é fechamento automático e você estiver escrevendo o conteúdo dentro do `section` elemento). Portanto, você precisa definir `TagMode` para `StartTagAndEndTag` para gravar a saída. Como alternativa, você pode comentar a linha que define `TagMode` e escrita de marcação com uma marca de fechamento. (Marcação de exemplo é fornecida posteriormente neste tutorial.)
    
    * O `$` (cifrão) na seguinte linha usa uma [cadeia de caracteres interpolada](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/interpolated-strings):
    
    ```cshtml
    $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
    ```

4.  Adicione a marcação a seguir à exibição *About.cshtml*. A marcação realçada exibe as informações do site.
    
    [!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]
    
    >[!NOTE]
    > Na marcação do Razor mostrada abaixo:
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]
    > 
    >O Razor reconhece que o atributo 'info' é uma classe, e não uma cadeia de caracteres, bem como que você deseja escrever o código C#. Qualquer atributo do auxiliar de marca que não seja uma cadeia de caracteres deve ser escrito sem o caractere '@'.
    
5.  Execute o aplicativo e navegue para a exibição About sobre para ver as informações do site.

    >[!NOTE]
    >Você pode usar a seguinte marcação com uma marca de fechamento e remova a linha com `TagMode.StartTagAndEndTag` no tag helper:
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]

## <a name="condition-tag-helper"></a>Tag helper condicional

O auxiliar de marca de condição renderiza a saída quando passado um valor true.

1.  Adicione a classe `ConditionTagHelper` a seguir à pasta *TagHelpers*.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

2.  Substitua o conteúdo do arquivo *Views/Home/Index.cshtml* pela seguinte marcação:

    ```cshtml
    @using AuthoringTagHelpers.Models
    @model WebsiteContext
    
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div>
        <h3>Information about our website (outdated):</h3>
        <website-information info=@Model />
        <div condition="@Model.Approved">
            <p>
                This website has <strong surround="em"> @Model.Approved </strong> been approved yet.
                Visit www.contoso.com for more information.
            </p>
        </div>
    </div>
    ```
    
3.  Substitua o método `Index` no controlador `Home` pelo seguinte código:

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

4.  Execute o aplicativo e navegue para a home page. A marcação no `div` condicional não será renderizada. Acrescente a cadeia de caracteres de consulta `?approved=true` à URL (por exemplo, `http://localhost:1235/Home/Index?approved=true`). `approved` é definido como verdadeiro e a marcação condicional será exibida.

>[!NOTE]
>Use o [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) operador para especificar o atributo de destino em vez de especificar uma cadeia de caracteres como você fez com o tag helper em negrito:
>
>[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
>O operador [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) protegerá o código, caso ele seja refatorado (recomendamos alterar o nome para `RedCondition`).

### <a name="avoid-tag-helper-conflicts"></a>Evitar conflitos de tag helper

Nesta seção, você escreve um par de tag helpers de vinculação automática. O primeiro substituirá a marcação que contém uma URL iniciada por HTTP para um HTML âncora marca que contém a mesma URL (e, portanto, resultando em um link de URL). O segundo fará o mesmo para uma URL começando com WWW.

Como esses dois auxiliares estão intimamente relacionados e você poderá refatorá-los no futuro, vamos mantê-los no mesmo arquivo.

1.  Adicione a classe `AutoLinkerHttpTagHelper` a seguir à pasta *TagHelpers*.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

    >[!NOTE]
    >A classe `AutoLinkerHttpTagHelper` é direcionada a elementos `p` e usa o [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) para criar a âncora.

2.  Adicione a seguinte marcação ao final do arquivo *Views/Home/Contact.cshtml*:

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

3.  Execute o aplicativo e verifique se o tag helper renderiza a âncora corretamente.

4.  Atualize a classe `AutoLinker` para incluir o `AutoLinkerWwwTagHelper` que converterá o texto www em uma marca de âncora que também contém o texto www original. O código atualizado é realçado abaixo:

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

5.  Execute o aplicativo. Observe o texto www é renderizado como um link, mas não o texto HTTP. Se você colocar um ponto de interrupção em ambas as classes, você pode ver que a classe do tag helper HTTP é executado primeira. O problema é que a saída do tag helper é armazenado em cache e quando o tag helper da Web é executado, ele substitui a saída do cache do tag helper HTTP. Posteriormente no tutorial, veremos como controlar a ordem de tag helpers executados no. Corrigiremos o código com o seguinte:

    [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

    >[!NOTE]
    >Na primeira edição os tag helpers de vinculação automática, você obteve o conteúdo do destino com o código a seguir:
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
    >
    >Ou seja, você chama `GetChildContentAsync` usando a `TagHelperOutput` passada para o método `ProcessAsync`. Conforme mencionado anteriormente, como a saída é armazenada em cache, o último auxiliar de marca a ser executado vence. Você corrigiu o problema com o seguinte código:
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
    >
    >O código acima verifica se o conteúdo foi modificado e, em caso afirmativo, ele obtém o conteúdo do buffer de saída.

6.  Execute o aplicativo e verifique se os dois links funcionam como esperado. Enquanto pode aparecer que nosso tag helper de vinculador automática está correto e completa, ela tem um problema sutil. Se o tag helper da Web é executado primeiro, os links da Web não estarão corretos. Atualize o código adicionando o `Order` sobrecarga para controlar a ordem em que a marca é executado no. O `Order` propriedade determina a ordem de execução em relação a outros tag helpers direcionando o mesmo elemento. O valor de ordem padrão é zero e instâncias com valores mais baixos são executadas primeiro.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]
    
    O código acima assegurará que o tag helper HTTP seja executado antes do tag helper WWW. Altere 'Order' para 'MaxValue' e verifique se a marcação gerada para a marcação WWW está incorreta.

## <a name="inspect-and-retrieve-child-content"></a>Inspecionar e recuperar o conteúdo filho

Os tag helpers fornecem várias propriedades para recuperar o conteúdo.

-  O resultado de `GetChildContentAsync` pode ser acrescentado ao `output.Content`.
-  Inspecione o resultado de `GetChildContentAsync` com `GetContent`.
-  Se você modificar `output.Content`, o corpo da TagHelper não será executado ou renderizado, a menos que você chame `GetChildContentAsync` como em nossa amostra de vinculador automático:

[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

-  Várias chamadas a `GetChildContentAsync` retornam o mesmo valor e não executam o corpo `TagHelper` novamente, a menos que você passe um parâmetro falso indicando para não usar o resultado armazenado em cache.
