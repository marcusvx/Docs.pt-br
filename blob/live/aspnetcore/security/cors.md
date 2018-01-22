---
title: "Habilitar solicitações entre origens (CORS)"
author: rick-anderson
description: "Este documento apresenta CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo do ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 05/17/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cors
ms.openlocfilehash: e6b49b9dde94cc7d035ea91b992a13df8cb8caf2
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="enabling-cross-origin-requests-cors"></a><span data-ttu-id="b3cdb-103">Habilitar solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="b3cdb-103">Enabling Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="b3cdb-104">Por [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b3cdb-104">By [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b3cdb-105">Segurança do navegador impede que uma página da web fazer solicitações do AJAX para outro domínio.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="b3cdb-106">Essa restrição é chamada de *política de mesma origem*e impede que um site mal-intencionado lendo dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b3cdb-107">No entanto, às vezes, convém permitir que outros sites fazer solicitações entre origens sua API da web.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-107">However, sometimes you might want to let other sites make cross-origin requests to your web API.</span></span>

<span data-ttu-id="b3cdb-108">[Entre o compartilhamento de recursos de origem](http://www.w3.org/TR/cors/) (CORS) é um padrão de W3C que permite que um servidor atenuar a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="b3cdb-109">Usando CORS, um servidor pode permitir explicitamente algumas solicitações entre origens durante a rejeição de outras pessoas.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="b3cdb-110">CORS é mais segura e mais flexível que técnicas anteriores como [JSONP](https://wikipedia.org/wiki/JSONP).</span><span class="sxs-lookup"><span data-stu-id="b3cdb-110">CORS is safer and more flexible than earlier techniques such as [JSONP](https://wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="b3cdb-111">Este tópico mostra como habilitar o CORS em um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-111">This topic shows how to enable CORS in an ASP.NET Core application.</span></span>

## <a name="what-is-same-origin"></a><span data-ttu-id="b3cdb-112">O que é "origem mesmo"?</span><span class="sxs-lookup"><span data-stu-id="b3cdb-112">What is "same origin"?</span></span>

<span data-ttu-id="b3cdb-113">Duas URLs têm a mesma origem se eles tiverem portas, hosts e esquemas idênticas.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-113">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="b3cdb-114">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="b3cdb-114">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="b3cdb-115">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-115">These two URLs have the same origin:</span></span>

* `http://example.com/foo.html`

* `http://example.com/bar.html`

<span data-ttu-id="b3cdb-116">Essas URLs têm diferentes origens que anterior dois:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-116">These URLs have different origins than the previous two:</span></span>

* <span data-ttu-id="b3cdb-117">`http://example.net`-Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="b3cdb-117">`http://example.net` - Different domain</span></span>

* <span data-ttu-id="b3cdb-118">`http://www.example.com/foo.html`-Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="b3cdb-118">`http://www.example.com/foo.html` - Different subdomain</span></span>

* <span data-ttu-id="b3cdb-119">`https://example.com/foo.html`-Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="b3cdb-119">`https://example.com/foo.html` - Different scheme</span></span>

* <span data-ttu-id="b3cdb-120">`http://example.com:9000/foo.html`-Porta diferente</span><span class="sxs-lookup"><span data-stu-id="b3cdb-120">`http://example.com:9000/foo.html` - Different port</span></span>

> [!NOTE]
> <span data-ttu-id="b3cdb-121">Internet Explorer não considera a porta ao comparar as origens.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-121">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="setting-up-cors"></a><span data-ttu-id="b3cdb-122">Configuração de CORS</span><span class="sxs-lookup"><span data-stu-id="b3cdb-122">Setting up CORS</span></span>

<span data-ttu-id="b3cdb-123">Para configurar CORS para o seu aplicativo, adicione o `Microsoft.AspNetCore.Cors` pacote ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-123">To set up CORS for your application add the `Microsoft.AspNetCore.Cors` package to your project.</span></span>

<span data-ttu-id="b3cdb-124">Adicione os serviços CORS em Startup.cs:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-124">Add the CORS services in Startup.cs:</span></span>

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a><span data-ttu-id="b3cdb-125">Habilitar o CORS com middleware</span><span class="sxs-lookup"><span data-stu-id="b3cdb-125">Enabling CORS with middleware</span></span>

<span data-ttu-id="b3cdb-126">Para habilitar o CORS para todo o seu aplicativo adicionar o middleware CORS ao seu pipeline de solicitação usando o `UseCors` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-126">To enable CORS for your entire application add the CORS middleware to your request pipeline using the `UseCors` extension method.</span></span> <span data-ttu-id="b3cdb-127">Observe que o middleware CORS deve preceder quaisquer pontos de extremidade definidos no aplicativo que você deseja dar suporte a solicitações entre origens (ex.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-127">Note that the CORS middleware must precede any defined endpoints in your app that you want to support cross-origin requests (ex.</span></span> <span data-ttu-id="b3cdb-128">antes de qualquer chamada para `UseMvc`).</span><span class="sxs-lookup"><span data-stu-id="b3cdb-128">before any call to `UseMvc`).</span></span>

<span data-ttu-id="b3cdb-129">Você pode especificar uma política entre origens ao adicionar o uso de middleware CORS a `CorsPolicyBuilder` classe.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-129">You can specify a cross-origin policy when adding the CORS middleware using the `CorsPolicyBuilder` class.</span></span> <span data-ttu-id="b3cdb-130">Há duas formas de fazer isso.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-130">There are two ways to do this.</span></span> <span data-ttu-id="b3cdb-131">A primeira é chamar UseCors com uma expressão lambda:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-131">The first is to call UseCors with a lambda:</span></span>

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

<span data-ttu-id="b3cdb-132">**Observação:** a URL deve ser especificada sem uma barra à direita (`/`).</span><span class="sxs-lookup"><span data-stu-id="b3cdb-132">**Note:** The URL must be specified without a trailing slash (`/`).</span></span> <span data-ttu-id="b3cdb-133">Se a URL termina com `/`, a comparação retornará `false` e nenhum cabeçalho será retornado.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-133">If the URL terminates with `/`, the comparison will return `false` and no header will be returned.</span></span>

<span data-ttu-id="b3cdb-134">O lambda leva um `CorsPolicyBuilder` objeto.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-134">The lambda takes a `CorsPolicyBuilder` object.</span></span> <span data-ttu-id="b3cdb-135">Você encontrará uma lista da [opções de configuração](#cors-policy-options) mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-135">You'll find a list of the [configuration options](#cors-policy-options) later in this topic.</span></span> <span data-ttu-id="b3cdb-136">Neste exemplo, a política permite que as solicitações entre origens de `http://example.com` e não há outras origens.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-136">In this example, the policy allows cross-origin requests from `http://example.com` and no other origins.</span></span>

<span data-ttu-id="b3cdb-137">Observe que CorsPolicyBuilder tem uma API fluente, portanto, é possível encadear chamadas de método:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-137">Note that CorsPolicyBuilder has a fluent API, so you can chain method calls:</span></span>

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

<span data-ttu-id="b3cdb-138">A segunda abordagem é definir uma ou mais políticas CORS nomeadas e, em seguida, selecione a política por nome em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-138">The second approach is to define one or more named CORS policies, and then select the policy by name at run time.</span></span>

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

<span data-ttu-id="b3cdb-139">Este exemplo adiciona uma política CORS denominada "AllowSpecificOrigin".</span><span class="sxs-lookup"><span data-stu-id="b3cdb-139">This example adds a CORS policy named "AllowSpecificOrigin".</span></span> <span data-ttu-id="b3cdb-140">Para selecionar a política, passe o nome para `UseCors`.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-140">To select the policy, pass the name to `UseCors`.</span></span>

## <a name="enabling-cors-in-mvc"></a><span data-ttu-id="b3cdb-141">Habilitando CORS no MVC</span><span class="sxs-lookup"><span data-stu-id="b3cdb-141">Enabling CORS in MVC</span></span>

<span data-ttu-id="b3cdb-142">Como alternativa, você pode usar MVC para aplicar CORS específicos por ação, por controlador ou globalmente para todos os controladores.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-142">You can alternatively use MVC to apply specific CORS per action, per controller, or globally for all controllers.</span></span> <span data-ttu-id="b3cdb-143">Ao usar o MVC para habilitar o CORS os mesmos serviços CORS são usados, mas o middleware CORS não é.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-143">When using MVC to enable CORS the same CORS services are used, but the CORS middleware is not.</span></span>

### <a name="per-action"></a><span data-ttu-id="b3cdb-144">Cada ação</span><span class="sxs-lookup"><span data-stu-id="b3cdb-144">Per action</span></span>

<span data-ttu-id="b3cdb-145">Para especificar uma política CORS para uma ação específica, adicione o `[EnableCors]` de atributo para a ação.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-145">To specify a CORS policy for a specific action add the `[EnableCors]` attribute to the action.</span></span> <span data-ttu-id="b3cdb-146">Especifique o nome da política.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-146">Specify the policy name.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a><span data-ttu-id="b3cdb-147">Por controlador</span><span class="sxs-lookup"><span data-stu-id="b3cdb-147">Per controller</span></span>

<span data-ttu-id="b3cdb-148">Para especificar a política CORS para um controlador específico, adicione o `[EnableCors]` de atributo para a classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-148">To specify the CORS policy for a specific controller add the `[EnableCors]` attribute to the controller class.</span></span> <span data-ttu-id="b3cdb-149">Especifique o nome da política.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-149">Specify the policy name.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a><span data-ttu-id="b3cdb-150">Globalmente</span><span class="sxs-lookup"><span data-stu-id="b3cdb-150">Globally</span></span>

<span data-ttu-id="b3cdb-151">Você pode habilitar o CORS globalmente para todos os controladores, adicionando o `CorsAuthorizationFilterFactory` filtro para a coleção de filtros globais:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-151">You can enable CORS globally for all controllers by adding the `CorsAuthorizationFilterFactory` filter to the global filter collection:</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

<span data-ttu-id="b3cdb-152">A ordem de precedência é: ação, controlador, global.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-152">The precedence order is: Action, controller, global.</span></span> <span data-ttu-id="b3cdb-153">Políticas de nível de ação têm precedência sobre políticas de nível de controlador e políticas no nível do controlador têm precedência sobre as políticas globais.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-153">Action-level policies take precedence over controller-level policies, and controller-level policies take precedence over global policies.</span></span>

### <a name="disable-cors"></a><span data-ttu-id="b3cdb-154">Desabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="b3cdb-154">Disable CORS</span></span>

<span data-ttu-id="b3cdb-155">Para desabilitar CORS para um controlador ou ação, use o `[DisableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-155">To disable CORS for a controller or action, use the `[DisableCors]` attribute.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a><span data-ttu-id="b3cdb-156">Opções de política CORS</span><span class="sxs-lookup"><span data-stu-id="b3cdb-156">CORS policy options</span></span>

<span data-ttu-id="b3cdb-157">Esta seção descreve as várias opções que podem ser definidas em uma política CORS.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-157">This section describes the various options that you can set in a CORS policy.</span></span>

* [<span data-ttu-id="b3cdb-158">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="b3cdb-158">Set the allowed origins</span></span>](#set-the-allowed-origins)

* [<span data-ttu-id="b3cdb-159">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="b3cdb-159">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)

* [<span data-ttu-id="b3cdb-160">Definir os cabeçalhos de solicitação permitido</span><span class="sxs-lookup"><span data-stu-id="b3cdb-160">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)

* [<span data-ttu-id="b3cdb-161">Definir os cabeçalhos de resposta exposto</span><span class="sxs-lookup"><span data-stu-id="b3cdb-161">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)

* [<span data-ttu-id="b3cdb-162">Credenciais nas solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="b3cdb-162">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)

* [<span data-ttu-id="b3cdb-163">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="b3cdb-163">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b3cdb-164">Para algumas opções pode ser útil ler [funciona como CORS](#how-cors-works) primeiro.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-164">For some options it may be helpful to read [How CORS works](#how-cors-works) first.</span></span>

### <a name="set-the-allowed-origins"></a><span data-ttu-id="b3cdb-165">Definir as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="b3cdb-165">Set the allowed origins</span></span>

<span data-ttu-id="b3cdb-166">Para permitir que um ou mais origens específicas:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-166">To allow one or more specific origins:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

<span data-ttu-id="b3cdb-167">Para permitir que todas as origens:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-167">To allow all origins:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

<span data-ttu-id="b3cdb-168">Considere cuidadosamente antes de permitir solicitações de qualquer origem.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-168">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="b3cdb-169">Isso significa que literalmente qualquer site pode fazer chamadas AJAX para sua API.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-169">It means that literally any website can make AJAX calls to your API.</span></span>

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b3cdb-170">Definir os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="b3cdb-170">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b3cdb-171">Para permitir que todos os métodos HTTP:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-171">To allow all HTTP methods:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

<span data-ttu-id="b3cdb-172">Isso afeta a solicitações preliminares e cabeçalho Access-controle-Allow-Methods.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-172">This affects pre-flight requests and Access-Control-Allow-Methods header.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b3cdb-173">Definir os cabeçalhos de solicitação permitido</span><span class="sxs-lookup"><span data-stu-id="b3cdb-173">Set the allowed request headers</span></span>

<span data-ttu-id="b3cdb-174">Uma solicitação de simulação de CORS pode incluir um cabeçalho Access-Control-Request-Headers, listando os cabeçalhos HTTP definidos pelo aplicativo (os chamados "author cabeçalhos de solicitação").</span><span class="sxs-lookup"><span data-stu-id="b3cdb-174">A CORS preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span>

<span data-ttu-id="b3cdb-175">Para cabeçalhos de específico de lista de permissões:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-175">To whitelist specific headers:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

<span data-ttu-id="b3cdb-176">Para permitir que todos os cabeçalhos de solicitação do autor:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-176">To allow all author request headers:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

<span data-ttu-id="b3cdb-177">Navegadores não são totalmente consistentes em como eles definidos Access-Control-Request-Headers.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-177">Browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="b3cdb-178">Se você definir cabeçalhos para algo diferente de "\*", você deve incluir pelo menos "aceitar", "content-type" e "origem", além de quaisquer cabeçalhos personalizados que você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-178">If you set headers to anything other than "\*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b3cdb-179">Definir os cabeçalhos de resposta exposto</span><span class="sxs-lookup"><span data-stu-id="b3cdb-179">Set the exposed response headers</span></span>

<span data-ttu-id="b3cdb-180">Por padrão, o navegador não expõe todos os cabeçalhos de resposta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-180">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="b3cdb-181">(Consulte [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-181">(See [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) The response headers that are available by default are:</span></span>

* <span data-ttu-id="b3cdb-182">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="b3cdb-182">Cache-Control</span></span>

* <span data-ttu-id="b3cdb-183">Content-Language</span><span class="sxs-lookup"><span data-stu-id="b3cdb-183">Content-Language</span></span>

* <span data-ttu-id="b3cdb-184">Tipo de conteúdo</span><span class="sxs-lookup"><span data-stu-id="b3cdb-184">Content-Type</span></span>

* <span data-ttu-id="b3cdb-185">Expirar</span><span class="sxs-lookup"><span data-stu-id="b3cdb-185">Expires</span></span>

* <span data-ttu-id="b3cdb-186">Last-Modified</span><span class="sxs-lookup"><span data-stu-id="b3cdb-186">Last-Modified</span></span>

* <span data-ttu-id="b3cdb-187">Pragma</span><span class="sxs-lookup"><span data-stu-id="b3cdb-187">Pragma</span></span>

<span data-ttu-id="b3cdb-188">A especificação CORS chama esses *cabeçalhos de resposta simples*.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-188">The CORS spec calls these *simple response headers*.</span></span> <span data-ttu-id="b3cdb-189">Para disponibilizar outros cabeçalhos para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-189">To make other headers available to the application:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b3cdb-190">Credenciais nas solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="b3cdb-190">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b3cdb-191">Credenciais requerem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-191">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b3cdb-192">Por padrão, o navegador não envia credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-192">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="b3cdb-193">As credenciais incluem cookies, bem como os esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-193">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="b3cdb-194">Para enviar as credenciais com uma solicitação entre origens, o cliente deve definir XMLHttpRequest.withCredentials como true.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-194">To send credentials with a cross-origin request, the client must set XMLHttpRequest.withCredentials to true.</span></span>

<span data-ttu-id="b3cdb-195">Usando XMLHttpRequest diretamente:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-195">Using XMLHttpRequest directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b3cdb-196">Em jQuery:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-196">In jQuery:</span></span>

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

<span data-ttu-id="b3cdb-197">Além disso, o servidor deve permitir que as credenciais.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-197">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="b3cdb-198">Para permitir que as credenciais de cross-origin:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-198">To allow cross-origin credentials:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

<span data-ttu-id="b3cdb-199">Agora, a resposta HTTP incluirá um cabeçalho Access-controle-Allow-Credentials, que informa ao navegador que o servidor permite que as credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-199">Now the HTTP response will include an Access-Control-Allow-Credentials header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b3cdb-200">Se o navegador envia as credenciais, mas a resposta não incluir um cabeçalho Access-controle-Allow-Credentials válido, o navegador não irá expor a resposta para o aplicativo e haverá falha na solicitação AJAX.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-200">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="b3cdb-201">Tenha muito cuidado sobre a permissão de credenciais entre origens, porque isso significa que um site da Web em outro domínio pode enviar credenciais do usuário conectado ao seu aplicativo em nome do usuário, sem o conhecimento do usuário.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-201">Be very careful about allowing cross-origin credentials, because it means a website at another domain can send a logged-in user’s credentials to your app on the user’s behalf, without the user being aware.</span></span> <span data-ttu-id="b3cdb-202">Os CORS especificação também estados que origens de configuração para "\*" (todas as origens) não é válido se o cabeçalho de acesso-controle-Allow-Credentials estiver presente.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-202">The CORS spec also states that setting origins to "\*" (all origins) is invalid if the Access-Control-Allow-Credentials header is present.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b3cdb-203">Definir o tempo de expiração de simulação</span><span class="sxs-lookup"><span data-stu-id="b3cdb-203">Set the preflight expiration time</span></span>

<span data-ttu-id="b3cdb-204">O cabeçalho de acesso-controle-Max-Age Especifica quanto tempo a resposta à solicitação de simulação pode ser armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-204">The Access-Control-Max-Age header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b3cdb-205">Para definir esse cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-205">To set this header:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name="cors-how-cors-works"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b3cdb-206">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="b3cdb-206">How CORS works</span></span>

<span data-ttu-id="b3cdb-207">Esta seção descreve o que acontece em uma solicitação CORS, o nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-207">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="b3cdb-208">É importante entender como CORS funciona, para que você pode configurar a política CORS corretamente e solucionar problemas se as coisas não funcionam conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-208">It’s important to understand how CORS works, so that you can configure your CORS policy correctly, and troubleshoot if things don’t work as you expect.</span></span>

<span data-ttu-id="b3cdb-209">A especificação CORS apresenta vários novos cabeçalhos HTTP que habilitam solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-209">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b3cdb-210">Se um navegador dá suporte a CORS, ele define esses cabeçalhos automaticamente para solicitações entre origens; Você não precisa fazer nada especial em seu código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-210">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don’t need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="b3cdb-211">Aqui está um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-211">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="b3cdb-212">O cabeçalho de "Origem" fornece o domínio do site que está fazendo a solicitação:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-212">The "Origin" header gives the domain of the site that is making the request:</span></span>

```
GET http://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: http://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: http://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="b3cdb-213">Se o servidor permite que a solicitação, ele define o cabeçalho Access-Control-Allow-Origin na resposta.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-213">If the server allows the request, it sets the Access-Control-Allow-Origin header in the response.</span></span> <span data-ttu-id="b3cdb-214">O valor desse cabeçalho corresponde o cabeçalho de origem da solicitação tanto é o valor de curinga "\*", o que significa que qualquer origem é permitida:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-214">The value of this header either matches the Origin header from the request, or is the wildcard value "\*", meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="b3cdb-215">Se a resposta não incluir o cabeçalho Access-Control-Allow-Origin, a solicitação AJAX falhará.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-215">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="b3cdb-216">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-216">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b3cdb-217">Mesmo se o servidor retornará uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-217">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="b3cdb-218">Solicitações de simulação</span><span class="sxs-lookup"><span data-stu-id="b3cdb-218">Preflight Requests</span></span>

<span data-ttu-id="b3cdb-219">Para algumas solicitações CORS, o navegador envia uma solicitação de adicional, chamada uma "solicitação de simulação," antes de enviar a solicitação real do recurso.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-219">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span> <span data-ttu-id="b3cdb-220">O navegador pode ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-220">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="b3cdb-221">O método de solicitação é GET, HEAD ou POST, e</span><span class="sxs-lookup"><span data-stu-id="b3cdb-221">The request method is GET, HEAD, or POST, and</span></span>

* <span data-ttu-id="b3cdb-222">O aplicativo não definir os cabeçalhos de solicitação diferente de idioma do conteúdo Accept, Accept-Language, Content-Type ou última--ID do evento, e</span><span class="sxs-lookup"><span data-stu-id="b3cdb-222">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, and</span></span>

* <span data-ttu-id="b3cdb-223">O cabeçalho Content-Type (se definido) é um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-223">The Content-Type header (if set) is one of the following:</span></span>

  * <span data-ttu-id="b3cdb-224">application/x-www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="b3cdb-224">application/x-www-form-urlencoded</span></span>

  * <span data-ttu-id="b3cdb-225">multipart/form-data</span><span class="sxs-lookup"><span data-stu-id="b3cdb-225">multipart/form-data</span></span>

  * <span data-ttu-id="b3cdb-226">texto/sem formatação</span><span class="sxs-lookup"><span data-stu-id="b3cdb-226">text/plain</span></span>

<span data-ttu-id="b3cdb-227">A regra sobre cabeçalhos de solicitação se aplica aos cabeçalhos que o aplicativo define chamando setRequestHeader no objeto XMLHttpRequest.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-227">The rule about request headers applies to headers that the application sets by calling setRequestHeader on the XMLHttpRequest object.</span></span> <span data-ttu-id="b3cdb-228">(A especificação CORS chama esses cabeçalhos de solicitação"autor".) A regra não se aplica aos cabeçalhos que pode definir o navegador, como o agente do usuário, o Host ou o comprimento do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-228">(The CORS specification calls these "author request headers".) The rule does not apply to headers the browser can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="b3cdb-229">Aqui está um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-229">Here is an example of a preflight request:</span></span>

```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="b3cdb-230">A solicitação de simulação usa o método HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-230">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="b3cdb-231">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-231">It includes two special headers:</span></span>

* <span data-ttu-id="b3cdb-232">Access-Control-Request-Method: O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-232">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>

* <span data-ttu-id="b3cdb-233">Access-Control-Request-Headers: Uma lista de cabeçalhos de solicitação que o aplicativo definido na solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-233">Access-Control-Request-Headers: A list of request headers that the application set on the actual request.</span></span> <span data-ttu-id="b3cdb-234">(Novamente, isso não inclui os cabeçalhos que define o navegador.)</span><span class="sxs-lookup"><span data-stu-id="b3cdb-234">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="b3cdb-235">Aqui está um exemplo de resposta, supondo que o servidor permite que a solicitação:</span><span class="sxs-lookup"><span data-stu-id="b3cdb-235">Here is an example response, assuming that the server allows the request:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="b3cdb-236">A resposta inclui um cabeçalho de acesso--permitir-métodos de controle que lista os métodos permitidos e, opcionalmente, um cabeçalho Access-Control-permitir-Headers, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-236">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="b3cdb-237">Se a solicitação de simulação for bem-sucedida, o navegador envia a solicitação real, conforme descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="b3cdb-237">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>