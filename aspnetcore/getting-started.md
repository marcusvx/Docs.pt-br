---
title: "Introdução ao ASP.NET Core 2.0"
author: rick-anderson
description: "Um tutorial rápido que cria e executa um aplicativo simples Olá, Mundo usando o ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/18/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: getting-started
ms.openlocfilehash: eb1fd748029743ca6991927cc95b612ed1975338
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2018
---
# <a name="get-started-with-aspnet-core"></a>Introdução ao ASP.NET Core

> [!NOTE]
> Essas instruções referem-se à última versão do ASP.NET Core. Deseja começar com uma versão anterior? Consulte [a versão 1.1 deste tutorial](xref:getting-started-1.1).

1. Instale o [.NET Core](https://www.microsoft.com/net/core/).

2. Crie um novo projeto .NET Core.

   No macOS e no Linux, abra uma janela de terminal. No Windows, abra um prompt de comando. Insira o seguinte comando:

    ```terminal
    dotnet new razor -o aspnetcoreapp
    ```
    
4. Execute o aplicativo.

    Use os seguintes comandos para executar o aplicativo:

    ```terminal
    cd aspnetcoreapp
    dotnet run
    ```

5. Navegue para [http://localhost:5000](http://localhost:5000)

6. Abra *Pages/About.cshtml* e modifique a página para exibir a mensagem "Olá, mundo! O horário no servidor é @DateTime.Now ":

    [!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]

7. Navegue para [http://localhost:5000/About](http://localhost:5000/About) e verifique as alterações.

### <a name="next-steps"></a>Próximas etapas

Para obter tutoriais de introdução, consulte [Tutoriais do ASP.NET Core](tutorials/index.md)

Para obter uma introdução aos conceitos e à arquitetura do ASP.NET Core, consulte [Introdução ao ASP.NET Core](index.md) e [Conceitos básicos do ASP.NET Core](fundamentals/index.md).

Um aplicativo ASP.NET Core pode usar a Biblioteca de Classes Base e o tempo de execução do .NET Core ou do .NET Framework. Para obter mais informações, consulte [Escolhendo entre o .NET Core e o .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).
