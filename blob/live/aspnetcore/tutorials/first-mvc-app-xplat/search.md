---
title: Adicionando uma pesquisa
author: rick-anderson
description: Mostra como adicionar uma pesquisa a um aplicativo ASP.NET Core MVC simples
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/search
ms.openlocfilehash: 2d8a18365a0d46d6468d708e1cd02def071309b7
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
[!INCLUDE[adding-model](../../includes/mvc-intro/search1.md)]

<span data-ttu-id="a7dec-103">Observação: o SQLlite diferencia maiúsculas de minúsculas e, portanto, você precisará pesquisar “Ghost” e não “ghost”.</span><span class="sxs-lookup"><span data-stu-id="a7dec-103">Note: SQLlite is case sensitive, so you'll need to search for "Ghost" and not "ghost".</span></span>

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

<span data-ttu-id="a7dec-104">Altere a marcação `<form>` na exibição *Views\movie\Index.cshtml* do Razor para especificar `method="get"`:</span><span class="sxs-lookup"><span data-stu-id="a7dec-104">Change the `<form>` tag in the *Views\movie\Index.cshtml* Razor view to specify `method="get"`:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
```

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
<span data-ttu-id="a7dec-105">[Anterior – Exibições e métodos do controlador](controller-methods-views.md)
[Próximo – Adicionar um campo](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="a7dec-105">[Previous - Controller methods and views](controller-methods-views.md)
[Next - Add a field](new-field.md)</span></span>  