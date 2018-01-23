---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-10
title: "Parte 10: Atualizações Final para navegação e Design de Site, conclusão | Microsoft Docs"
author: jongalloway
description: "Esta série de tutoriais fornece detalhes sobre todas as etapas realizadas para compilar o aplicativo de exemplo do repositório de música do ASP.NET MVC. Parte 10 abrange atualizações Final para navegação e S..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: 0c6e4c2f-fcdb-4978-9656-1990c6f15727
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-10
msc.type: authoredcontent
ms.openlocfilehash: af08039de2d810948b9ab64974111b0346c7fa0f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
<a name="part-10-final-updates-to-navigation-and-site-design-conclusion"></a><span data-ttu-id="05198-104">Parte 10: Atualizações Final para navegação e Design de Site, conclusão</span><span class="sxs-lookup"><span data-stu-id="05198-104">Part 10: Final Updates to Navigation and Site Design, Conclusion</span></span>
====================
<span data-ttu-id="05198-105">por [Jon Galloway](https://github.com/jongalloway)</span><span class="sxs-lookup"><span data-stu-id="05198-105">by [Jon Galloway](https://github.com/jongalloway)</span></span>

> <span data-ttu-id="05198-106">O repositório de música MVC é um aplicativo tutorial que apresenta e explica passo a passo sobre como usar o ASP.NET MVC e o Visual Studio para desenvolvimento na web.</span><span class="sxs-lookup"><span data-stu-id="05198-106">The MVC Music Store is a tutorial application that introduces and explains step-by-step how to use ASP.NET MVC and Visual Studio for web development.</span></span>  
>   
> <span data-ttu-id="05198-107">O repositório de música MVC é uma implementação de repositório de exemplo leve que vende álbuns de música online e implementa a administração de site básico, entrada do usuário e funcionalidade do carrinho de compras.</span><span class="sxs-lookup"><span data-stu-id="05198-107">The MVC Music Store is a lightweight sample store implementation which sells music albums online, and implements basic site administration, user sign-in, and shopping cart functionality.</span></span>  
>   
> <span data-ttu-id="05198-108">Esta série de tutoriais fornece detalhes sobre todas as etapas realizadas para compilar o aplicativo de exemplo do repositório de música do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="05198-108">This tutorial series details all of the steps taken to build the ASP.NET MVC Music Store sample application.</span></span> <span data-ttu-id="05198-109">Parte 10 abrange atualizações Final para navegação e Design de Site, a conclusão.</span><span class="sxs-lookup"><span data-stu-id="05198-109">Part 10 covers Final Updates to Navigation and Site Design, Conclusion.</span></span>


<span data-ttu-id="05198-110">Concluímos toda a funcionalidade principal para o nosso site, mas ainda temos alguns recursos para adicionar a navegação do site, a página inicial e a página Procurar armazenamento.</span><span class="sxs-lookup"><span data-stu-id="05198-110">We've completed all the major functionality for our site, but we still have some features to add to the site navigation, the home page, and the Store Browse page.</span></span>

## <a name="creating-the-shopping-cart-summary-partial-view"></a><span data-ttu-id="05198-111">Criando a exibição parcial resumo de carrinho compras</span><span class="sxs-lookup"><span data-stu-id="05198-111">Creating the Shopping Cart Summary Partial View</span></span>

<span data-ttu-id="05198-112">Queremos expor o número de itens no carrinho de compras do usuário em todo o site.</span><span class="sxs-lookup"><span data-stu-id="05198-112">We want to expose the number of items in the user's shopping cart across the entire site.</span></span>

![](mvc-music-store-part-10/_static/image1.png)

<span data-ttu-id="05198-113">Podemos facilmente implementar isso criando uma exibição parcial que é adicionada ao nosso Site.master.</span><span class="sxs-lookup"><span data-stu-id="05198-113">We can easily implement this by creating a partial view which is added to our Site.master.</span></span>

<span data-ttu-id="05198-114">Como mostrado anteriormente, o controlador ShoppingCart inclui um método de ação de CartSummary que retorna uma exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="05198-114">As shown previously, the ShoppingCart controller includes a CartSummary action method which returns a partial view:</span></span>

[!code-csharp[Main](mvc-music-store-part-10/samples/sample1.cs)]

<span data-ttu-id="05198-115">Para criar a exibição parcial CartSummary, com o botão direito na pasta exibições/ShoppingCart e selecione Adicionar modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="05198-115">To create the CartSummary partial view, right-click on the Views/ShoppingCart folder and select Add View.</span></span> <span data-ttu-id="05198-116">Nomeie a exibição CartSummary e marque a caixa de seleção "Criar uma exibição parcial", conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="05198-116">Name the view CartSummary and check the "Create a partial view" checkbox as shown below.</span></span>

![](mvc-music-store-part-10/_static/image2.png)

<span data-ttu-id="05198-117">A exibição parcial CartSummary é realmente simple – é apenas um link para a exibição do índice ShoppingCart que mostra o número de itens no carrinho.</span><span class="sxs-lookup"><span data-stu-id="05198-117">The CartSummary partial view is really simple - it's just a link to the ShoppingCart Index view which shows the number of items in the cart.</span></span> <span data-ttu-id="05198-118">O código completo para CartSummary.cshtml é o seguinte:</span><span class="sxs-lookup"><span data-stu-id="05198-118">The complete code for CartSummary.cshtml is as follows:</span></span>

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample2.cshtml)]

<span data-ttu-id="05198-119">Podemos pode incluir uma exibição parcial em qualquer página no site, incluindo o mestre de Site, usando o método Html.RenderAction.</span><span class="sxs-lookup"><span data-stu-id="05198-119">We can include a partial view in any page in the site, including the Site master, by using the Html.RenderAction method.</span></span> <span data-ttu-id="05198-120">RenderAction exige especificar o nome da ação ("CartSummary") e o nome do controlador ("ShoppingCart") como abaixo.</span><span class="sxs-lookup"><span data-stu-id="05198-120">RenderAction requires us to specify the Action Name ("CartSummary") and the Controller Name ("ShoppingCart") as below.</span></span>

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample3.cshtml)]

<span data-ttu-id="05198-121">Antes de adicionar isso ao site de Layout, vamos também criar o Menu de gênero para que possamos fazer todos os nossos Site.master atualizações ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="05198-121">Before adding this to the site Layout, we will also create the Genre Menu so we can make all of our Site.master updates at one time.</span></span>

## <a name="creating-the-genre-menu-partial-view"></a><span data-ttu-id="05198-122">Criando a exibição parcial do Menu de gênero</span><span class="sxs-lookup"><span data-stu-id="05198-122">Creating the Genre Menu Partial View</span></span>

<span data-ttu-id="05198-123">Podemos pode fazer muito mais fácil para os usuários navegar por meio da loja, adicionando um Menu de gênero que lista todos os gêneros disponíveis na loja.</span><span class="sxs-lookup"><span data-stu-id="05198-123">We can make it a lot easier for our users to navigate through the store by adding a Genre Menu which lists all the Genres available in our store.</span></span>

![](mvc-music-store-part-10/_static/image3.png)

<span data-ttu-id="05198-124">A seguir, o mesmo etapas também criam uma exibição parcial GenreMenu e, em seguida, podemos adicionar-os com o mestre de Site.</span><span class="sxs-lookup"><span data-stu-id="05198-124">We will follow the same steps also create a GenreMenu partial view, and then we can add them both to the Site master.</span></span> <span data-ttu-id="05198-125">Primeiro, adicione a seguinte ação de controlador GenreMenu para o StoreController:</span><span class="sxs-lookup"><span data-stu-id="05198-125">First, add the following GenreMenu controller action to the StoreController:</span></span>

[!code-csharp[Main](mvc-music-store-part-10/samples/sample4.cs)]

<span data-ttu-id="05198-126">Essa ação retorna uma lista de gêneros que será exibido quando a exibição parcial, vamos criar em seguida.</span><span class="sxs-lookup"><span data-stu-id="05198-126">This action returns a list of Genres which will be displayed by the partial view, which we will create next.</span></span>

<span data-ttu-id="05198-127">*Observação: Adicionamos o atributo [ChildActionOnly] para esta ação de controlador, o que indica que desejamos somente essa ação a ser usado em uma exibição parcial. Esse atributo impedirá que a ação de controlador que está sendo executado, navegando para /Store/GenreMenu. Isso não é necessário para exibições parciais, mas é uma boa prática, desde que você deseja certificar-se de que nossas ações do controlador são usadas como pretendemos. Estamos também retornando PartialView em vez de exibição, que permite que o mecanismo de exibição sabe que ele não deve usar o Layout para este modo de exibição, pois ele está sendo incluído em outros modos de exibição.*</span><span class="sxs-lookup"><span data-stu-id="05198-127">*Note: We have added the [ChildActionOnly] attribute to this controller action, which indicates that we only want this action to be used from a Partial View. This attribute will prevent the controller action from being executed by browsing to /Store/GenreMenu. This isn't required for partial views, but it is a good practice, since we want to make sure our controller actions are used as we intend. We are also returning PartialView rather than View, which lets the view engine know that it shouldn't use the Layout for this view, as it is being included in other views.*</span></span>

<span data-ttu-id="05198-128">Clique na ação do controlador GenreMenu e criar uma exibição parcial chamada GenreMenu que é fortemente tipado usando a classe de dados de exibição de gênero, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="05198-128">Right-click on the GenreMenu controller action and create a partial view named GenreMenu which is strongly typed using the Genre view data class as shown below.</span></span>

![](mvc-music-store-part-10/_static/image4.png)

<span data-ttu-id="05198-129">Atualize o código de exibição para a exibição parcial GenreMenu exibir os itens usando uma lista não ordenada da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="05198-129">Update the view code for the GenreMenu partial view to display the items using an unordered list as follows.</span></span>

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample5.cshtml)]

## <a name="updating-site-layout-to-display-our-partial-views"></a><span data-ttu-id="05198-130">Atualizando o Layout do Site para exibir nossa exibições parciais</span><span class="sxs-lookup"><span data-stu-id="05198-130">Updating Site Layout to display our Partial Views</span></span>

<span data-ttu-id="05198-131">Podemos adicionar nossas exibições parciais para o Layout do Site (/exibições/compartilhado/\_cshtml) chamando Html.RenderAction().</span><span class="sxs-lookup"><span data-stu-id="05198-131">We can add our partial views to the Site Layout (/Views/Shared/\_Layout.cshtml) by calling Html.RenderAction().</span></span> <span data-ttu-id="05198-132">Vamos adicioná-los no, bem como algumas marcações adicionais para exibi-los, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="05198-132">We'll add them both in, as well as some additional markup to display them, as shown below:</span></span>

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample6.cshtml)]

<span data-ttu-id="05198-133">Agora quando o aplicativo é executado, veremos gênero na área de navegação à esquerda e o resumo do carrinho na parte superior.</span><span class="sxs-lookup"><span data-stu-id="05198-133">Now when we run the application, we will see the Genre in the left navigation area and the Cart Summary at the top.</span></span>

## <a name="update-to-the-store-browse-page"></a><span data-ttu-id="05198-134">Atualizar para a página Procurar repositório</span><span class="sxs-lookup"><span data-stu-id="05198-134">Update to the Store Browse page</span></span>

<span data-ttu-id="05198-135">A página Procurar armazenamento está funcionando, mas não parece muito bom.</span><span class="sxs-lookup"><span data-stu-id="05198-135">The Store Browse page is functional, but doesn't look very good.</span></span> <span data-ttu-id="05198-136">Podemos atualizar a página para mostrar os álbuns em um layout melhor ao atualizar o código de exibição (encontrado no /Views/Store/Browse.cshtml) da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="05198-136">We can update the page to show the albums in a better layout by updating the view code (found in /Views/Store/Browse.cshtml) as follows:</span></span>

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample7.cshtml)]

<span data-ttu-id="05198-137">Aqui estamos fazendo uso de URL em vez de ActionLink para que podemos aplicar formatação especial para o link para incluir a arte do álbum.</span><span class="sxs-lookup"><span data-stu-id="05198-137">Here we are making use of Url.Action rather than Html.ActionLink so that we can apply special formatting to the link to include the album artwork.</span></span>

<span data-ttu-id="05198-138">*Observação: É estiver exibindo uma capa de álbum genérica para esses álbuns. Essas informações são armazenadas no banco de dados e pode ser editadas através do Gerenciador de armazenamento. Você está bem-vindo ao adicionar sua própria arte final.*</span><span class="sxs-lookup"><span data-stu-id="05198-138">*Note: We are displaying a generic album cover for these albums. This information is stored in the database and is editable via the Store Manager. You are welcome to add your own artwork.*</span></span>

<span data-ttu-id="05198-139">Agora quando podemos procurar um gênero, veremos os álbuns mostrados em uma grade com a arte do álbum.</span><span class="sxs-lookup"><span data-stu-id="05198-139">Now when we browse to a Genre, we will see the albums shown in a grid with the album artwork.</span></span>

![](mvc-music-store-part-10/_static/image5.png)

## <a name="updating-the-home-page-to-show-top-selling-albums"></a><span data-ttu-id="05198-140">Atualizando a página inicial para mostrar superior álbuns de venda</span><span class="sxs-lookup"><span data-stu-id="05198-140">Updating the Home Page to show Top Selling Albums</span></span>

<span data-ttu-id="05198-141">Queremos que nossa vendido álbuns na home page de aumentar as vendas de recursos.</span><span class="sxs-lookup"><span data-stu-id="05198-141">We want to feature our top selling albums on the home page to increase sales.</span></span> <span data-ttu-id="05198-142">Faremos algumas atualizações nosso HomeController para lidar com isso e adicione alguns elementos de gráficos adicionais.</span><span class="sxs-lookup"><span data-stu-id="05198-142">We'll make some updates to our HomeController to handle that, and add in some additional graphics as well.</span></span>

<span data-ttu-id="05198-143">Primeiro, adicionaremos uma propriedade de navegação para nossa classe álbum para que EntityFramework sabe que estão associados.</span><span class="sxs-lookup"><span data-stu-id="05198-143">First, we'll add a navigation property to our Album class so that EntityFramework knows that they're associated.</span></span> <span data-ttu-id="05198-144">As últimas linhas do nosso **álbum** classe agora deve se parecer com:</span><span class="sxs-lookup"><span data-stu-id="05198-144">The last few lines of our **Album** class should now look like this:</span></span>

[!code-csharp[Main](mvc-music-store-part-10/samples/sample8.cs)]

<span data-ttu-id="05198-145">*Observação: Será necessário adicionar um usando a instrução para trazer o namespace de Generic.*</span><span class="sxs-lookup"><span data-stu-id="05198-145">*Note: This will require adding a using statement to bring in the System.Collections.Generic namespace.*</span></span>

<span data-ttu-id="05198-146">Primeiro, vamos adicionar um campo storeDB e o MvcMusicStore.Models usando as instruções, como em nossos outros controladores.</span><span class="sxs-lookup"><span data-stu-id="05198-146">First, we'll add a storeDB field and the MvcMusicStore.Models using statements, as in our other controllers.</span></span> <span data-ttu-id="05198-147">Em seguida, adicionaremos o seguinte método ao qual consulta nosso banco de dados para localizar os principais álbuns de vendas de acordo com a OrderDetails HomeController.</span><span class="sxs-lookup"><span data-stu-id="05198-147">Next, we'll add the following method to the HomeController which queries our database to find top selling albums according to OrderDetails.</span></span>

[!code-csharp[Main](mvc-music-store-part-10/samples/sample9.cs)]

<span data-ttu-id="05198-148">Isso é um método privado, pois não queremos disponibilizá-lo como uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="05198-148">This is a private method, since we don't want to make it available as a controller action.</span></span> <span data-ttu-id="05198-149">Incluímos-lo no HomeController para manter a simplicidade, mas você é incentivado a mover sua lógica de negócios em classes de serviço separado conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="05198-149">We are including it in the HomeController for simplicity, but you are encouraged to move your business logic into separate service classes as appropriate.</span></span>

<span data-ttu-id="05198-150">Com isso em vigor, podemos atualizar a ação de controlador de índice para consultar os 5 principais álbuns e retorná-los para o modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="05198-150">With that in place, we can update the Index controller action to query the top 5 selling albums and return them to the view.</span></span>

[!code-csharp[Main](mvc-music-store-part-10/samples/sample10.cs)]

<span data-ttu-id="05198-151">O código completo para o HomeController atualizado é conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="05198-151">The complete code for the updated HomeController is as shown below.</span></span>

[!code-csharp[Main](mvc-music-store-part-10/samples/sample11.cs)]

<span data-ttu-id="05198-152">Por fim, precisaremos atualizar nossa visualização de índice de início para que ele pode exibir uma lista de álbuns atualizando o tipo de modelo e adicionar a lista de álbum na parte inferior.</span><span class="sxs-lookup"><span data-stu-id="05198-152">Finally, we'll need to update our Home Index view so that it can display a list of albums by updating the Model type and adding the album list to the bottom.</span></span> <span data-ttu-id="05198-153">Nós o conduziremos essa oportunidade para também pode adicionar um título e uma seção de promoção para a página.</span><span class="sxs-lookup"><span data-stu-id="05198-153">We will take this opportunity to also add a heading and a promotion section to the page.</span></span>

[!code-cshtml[Main](mvc-music-store-part-10/samples/sample12.cshtml)]

<span data-ttu-id="05198-154">Agora quando o aplicativo é executado, veremos nossa página atualizada com principais álbuns de vendas e nossa mensagem promocional.</span><span class="sxs-lookup"><span data-stu-id="05198-154">Now when we run the application, we'll see our updated home page with top selling albums and our promotional message.</span></span>

![](mvc-music-store-part-10/_static/image1.jpg)

## <a name="conclusion"></a><span data-ttu-id="05198-155">Conclusão</span><span class="sxs-lookup"><span data-stu-id="05198-155">Conclusion</span></span>

<span data-ttu-id="05198-156">Já vimos que esse ASP.NET MVC torna mais fácil para criar um site sofisticado com acesso de banco de dados, associação, AJAX, etc.</span><span class="sxs-lookup"><span data-stu-id="05198-156">We've seen that that ASP.NET MVC makes it easy to create a sophisticated website with database access, membership, AJAX, etc.</span></span> <span data-ttu-id="05198-157">muito rapidamente.</span><span class="sxs-lookup"><span data-stu-id="05198-157">pretty quickly.</span></span> <span data-ttu-id="05198-158">Esperamos que este tutorial lhe forneceu as ferramentas que você precisa para começar a criar seu próprio ASP.NET MVC aplicativos!</span><span class="sxs-lookup"><span data-stu-id="05198-158">Hopefully this tutorial has given you the tools you need to get started building your own ASP.NET MVC applications!</span></span>


>[!div class="step-by-step"]
[<span data-ttu-id="05198-159">Anterior</span><span class="sxs-lookup"><span data-stu-id="05198-159">Previous</span></span>](mvc-music-store-part-9.md)