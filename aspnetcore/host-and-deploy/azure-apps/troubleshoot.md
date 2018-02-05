---
title: "Solucionar problemas de ASP.NET Core no serviço de aplicativo do Azure"
author: guardrex
description: "Saiba como diagnosticar problemas com implantações do Serviço de Aplicativo do Azure do ASP.NET Core."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 144af8e93bb935d07fd064d5f45b40faea4a2664
ms.sourcegitcommit: 7a87d66cf1d01febe6635c7306f2f679434901d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="41a64-103">Solucionar problemas de ASP.NET Core no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="41a64-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="41a64-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="41a64-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="41a64-105">Este artigo fornece instruções sobre como diagnosticar uma ASP.NET Core problema de inicialização do aplicativo usando ferramentas de diagnóstico do serviço de aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="41a64-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="41a64-106">Para avisos de solução de problemas adicionais, consulte [visão geral do serviço de aplicativo do Azure diagnostics](/azure/app-service/app-service-diagnostics) e [como: monitorar aplicativos no serviço de aplicativo do Azure](/azure/app-service/web-sites-monitor) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="41a64-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="41a64-107">Erros de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="41a64-107">App startup errors</span></span>

<span data-ttu-id="41a64-108">**502.5 falha de processo**</span><span class="sxs-lookup"><span data-stu-id="41a64-108">**502.5 Process Failure**</span></span>  
<span data-ttu-id="41a64-109">O processo de trabalho falha.</span><span class="sxs-lookup"><span data-stu-id="41a64-109">The worker process fails.</span></span> <span data-ttu-id="41a64-110">O aplicativo não foi iniciada.</span><span class="sxs-lookup"><span data-stu-id="41a64-110">The app doesn't start.</span></span>

<span data-ttu-id="41a64-111">O [ASP.NET Core módulo](xref:fundamentals/servers/aspnet-core-module) tentativas de iniciar o processo de trabalho, mas ele não pode ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="41a64-111">The [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="41a64-112">Examinar o Log de eventos do aplicativo geralmente ajuda a solucionar esse tipo de problema.</span><span class="sxs-lookup"><span data-stu-id="41a64-112">Examining the Application Event Log often helps troubleshoot this type of problem.</span></span> <span data-ttu-id="41a64-113">Acessar o log é explicado no [Log de eventos do aplicativo](#application-event-log) seção.</span><span class="sxs-lookup"><span data-stu-id="41a64-113">Accessing the log is explained in the [Application Event Log](#application-event-log) section.</span></span>

<span data-ttu-id="41a64-114">O *502.5 falha no processo* página de erro é retornada quando um aplicativo configurado incorretamente faz com que a falha do processo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="41a64-114">The *502.5 Process Failure* error page is returned when a misconfigured app causes the worker process to fail:</span></span>

![Janela do navegador mostrando a página de falha do processo 502.5](troubleshoot/_static/process-failure-page.png)

<span data-ttu-id="41a64-116">**Erro de servidor interno 500**</span><span class="sxs-lookup"><span data-stu-id="41a64-116">**500 Internal Server Error**</span></span>  
<span data-ttu-id="41a64-117">O aplicativo é iniciado, mas um erro impede que o servidor de atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="41a64-117">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="41a64-118">Esse erro ocorre no código do aplicativo durante a inicialização ou durante a criação de uma resposta.</span><span class="sxs-lookup"><span data-stu-id="41a64-118">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="41a64-119">A resposta não poderá conter nenhum conteúdo ou a resposta pode ser exibido como um *500 Erro interno do servidor* no navegador.</span><span class="sxs-lookup"><span data-stu-id="41a64-119">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="41a64-120">O Log de eventos do aplicativo geralmente indica que o aplicativo é iniciado normalmente.</span><span class="sxs-lookup"><span data-stu-id="41a64-120">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="41a64-121">Da perspectiva do servidor, que está correta.</span><span class="sxs-lookup"><span data-stu-id="41a64-121">From the server's perspective, that's correct.</span></span> <span data-ttu-id="41a64-122">O aplicativo foi iniciado, mas não pode gerar uma resposta válida.</span><span class="sxs-lookup"><span data-stu-id="41a64-122">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="41a64-123">[Execute o aplicativo no console do Kudu](#run-the-app-in-the-kudu-console) ou [habilite o log do módulo do ASP.NET Core stdout](#aspnet-core-module-stdout-log) para solucionar o problema.</span><span class="sxs-lookup"><span data-stu-id="41a64-123">[Run the app in the Kudu console](#run-the-app-in-the-kudu-console) or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="41a64-124">Solucionar problemas de inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="41a64-124">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="41a64-125">Log de eventos do aplicativo</span><span class="sxs-lookup"><span data-stu-id="41a64-125">Application Event Log</span></span>

<span data-ttu-id="41a64-126">Para acessar o Log de eventos do aplicativo, use o **diagnosticar e resolver problemas** folha no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="41a64-126">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal :</span></span>

1. <span data-ttu-id="41a64-127">No portal do Azure, abra a folha do aplicativo no **serviços de aplicativos** folha.</span><span class="sxs-lookup"><span data-stu-id="41a64-127">In the Azure portal, open the app's blade in the **App Services** blade.</span></span>
1. <span data-ttu-id="41a64-128">Selecione o **diagnosticar e resolver problemas** folha.</span><span class="sxs-lookup"><span data-stu-id="41a64-128">Select the **Diagnose and solve problems** blade.</span></span>
1. <span data-ttu-id="41a64-129">Em **Selecionar categoria de problema**, selecione o **aplicativo Web para baixo** botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-129">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="41a64-130">Em **soluções sugeridas**, abra o painel para **abrir Logs de eventos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="41a64-130">Under **Suggested Solutions**, open the pane for **Open Application Event Logs**.</span></span> <span data-ttu-id="41a64-131">Selecione o **abrir Logs de eventos de aplicativo** botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-131">Select the **Open Application Event Logs** button.</span></span>
1. <span data-ttu-id="41a64-132">Examine o erro mais recente fornecido pelo *IIS AspNetCoreModule* no **fonte** coluna.</span><span class="sxs-lookup"><span data-stu-id="41a64-132">Examine the latest error provided by the *IIS AspNetCoreModule* in the **Source** column.</span></span>

<span data-ttu-id="41a64-133">Uma alternativa ao uso de **diagnosticar e resolver problemas** folha é examinar o arquivo de Log de eventos do aplicativo diretamente usando [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="41a64-133">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="41a64-134">Selecione o **ferramentas avançadas de** folha no **ferramentas de desenvolvimento** área.</span><span class="sxs-lookup"><span data-stu-id="41a64-134">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="41a64-135">Selecione o **vá&rarr;**  botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-135">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="41a64-136">O console do Kudu é aberto em uma janela ou nova guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="41a64-136">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="41a64-137">Usando a barra de navegação na parte superior da página, abra **console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="41a64-137">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="41a64-138">Abra o **LogFiles** pasta.</span><span class="sxs-lookup"><span data-stu-id="41a64-138">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="41a64-139">Selecione o ícone de lápis ao lado de *eventlog.xml* arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-139">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="41a64-140">Examine o log.</span><span class="sxs-lookup"><span data-stu-id="41a64-140">Examine the log.</span></span> <span data-ttu-id="41a64-141">Role até o final do log para ver os eventos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="41a64-141">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="41a64-142">Execute o aplicativo no console do Kudu</span><span class="sxs-lookup"><span data-stu-id="41a64-142">Run the app in the Kudu console</span></span>

<span data-ttu-id="41a64-143">Muitos erros de inicialização não produzem informações úteis no Log de eventos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41a64-143">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="41a64-144">Você pode executar o aplicativo no [Kudu](https://github.com/projectkudu/kudu/wiki) Console remoto de execução para descobrir o erro:</span><span class="sxs-lookup"><span data-stu-id="41a64-144">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="41a64-145">Selecione o **ferramentas avançadas de** folha no **ferramentas de desenvolvimento** área.</span><span class="sxs-lookup"><span data-stu-id="41a64-145">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="41a64-146">Selecione o **vá&rarr;**  botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-146">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="41a64-147">O console do Kudu é aberto em uma janela ou nova guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="41a64-147">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="41a64-148">Usando a barra de navegação na parte superior da página, abra **console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="41a64-148">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="41a64-149">Abra as pastas no caminho **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="41a64-149">Open the folders to the path **site** > **wwwroot**.</span></span>
1. <span data-ttu-id="41a64-150">No console do, execute o aplicativo com a execução de assembly do aplicativo com *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="41a64-150">In the console, run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="41a64-151">No comando a seguir, substitua o nome do assembly do aplicativo para `<assembly_name>`:</span><span class="sxs-lookup"><span data-stu-id="41a64-151">In the following command, substitute the name of the app's assembly for `<assembly_name>`:</span></span>
   ```console
   dotnet .\<assembly_name>.dll
   ```
1. <span data-ttu-id="41a64-152">A saída do aplicativo, mostrando os erros do console está conectado ao console Kudu.</span><span class="sxs-lookup"><span data-stu-id="41a64-152">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="41a64-153">Log de stdout de módulo principal do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="41a64-153">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="41a64-154">O módulo do ASP.NET Core stdout geralmente registra mensagens de erro úteis não encontradas no Log de eventos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41a64-154">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="41a64-155">Para habilitar e exibir logs de stdout:</span><span class="sxs-lookup"><span data-stu-id="41a64-155">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="41a64-156">Navegue até o **diagnosticar e resolver problemas** folha no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="41a64-156">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="41a64-157">Em **Selecionar categoria de problema**, selecione o **aplicativo Web para baixo** botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-157">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="41a64-158">Em **soluções sugeridas** > **habilitar o redirecionamento de Log Stdout**, selecione o botão de **abrir o Console do Kudu para editar o Web. config**.</span><span class="sxs-lookup"><span data-stu-id="41a64-158">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="41a64-159">No Kudu **Console diagnóstico**, abra as pastas no caminho **site** > **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="41a64-159">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="41a64-160">Role para baixo para revelar o *Web. config* arquivo na parte inferior da lista.</span><span class="sxs-lookup"><span data-stu-id="41a64-160">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="41a64-161">Clique no ícone de lápis ao lado de *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-161">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="41a64-162">Definir **stdoutLogEnabled** para `true` e altere o **stdoutLogFile** caminho: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="41a64-162">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="41a64-163">Selecione **salvar** para salvar o documento atualizado *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-163">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="41a64-164">Fazer uma solicitação para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41a64-164">Make a request to the app.</span></span>
1. <span data-ttu-id="41a64-165">Retorne ao portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="41a64-165">Return to the Azure portal.</span></span> <span data-ttu-id="41a64-166">Selecione o **ferramentas avançadas de** folha no **ferramentas de desenvolvimento** área.</span><span class="sxs-lookup"><span data-stu-id="41a64-166">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="41a64-167">Selecione o **vá&rarr;**  botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-167">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="41a64-168">O console do Kudu é aberto em uma janela ou nova guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="41a64-168">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="41a64-169">Usando a barra de navegação na parte superior da página, abra **console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="41a64-169">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="41a64-170">Selecione o **LogFiles** pasta.</span><span class="sxs-lookup"><span data-stu-id="41a64-170">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="41a64-171">Inspecione o **modificadas** coluna e selecione o ícone de lápis para editar o stdout, faça logon com a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="41a64-171">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="41a64-172">Quando o arquivo de log é aberto, o erro é exibido.</span><span class="sxs-lookup"><span data-stu-id="41a64-172">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="41a64-173">**Importante!**</span><span class="sxs-lookup"><span data-stu-id="41a64-173">**Important!**</span></span> <span data-ttu-id="41a64-174">Desabilite stdout registro em log quando o problema for solucionado.</span><span class="sxs-lookup"><span data-stu-id="41a64-174">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="41a64-175">No Kudu **Console diagnóstico**, retorne para o caminho **site** > **wwwroot** para revelar o *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-175">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="41a64-176">Abra o **Web. config** arquivo novamente, selecionando o ícone de lápis.</span><span class="sxs-lookup"><span data-stu-id="41a64-176">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="41a64-177">Definir **stdoutLogEnabled** para `false`.</span><span class="sxs-lookup"><span data-stu-id="41a64-177">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="41a64-178">Selecione **salvar** para salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-178">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="41a64-179">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou servidor.</span><span class="sxs-lookup"><span data-stu-id="41a64-179">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="41a64-180">Não há nenhum limite no tamanho do arquivo de log ou o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="41a64-180">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="41a64-181">Para log de rotina no aplicativo do ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e gira logs.</span><span class="sxs-lookup"><span data-stu-id="41a64-181">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="41a64-182">Para obter mais informações, consulte [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="41a64-182">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="41a64-183">Erros comuns de inicialização</span><span class="sxs-lookup"><span data-stu-id="41a64-183">Common startup errors</span></span> 

<span data-ttu-id="41a64-184">Consulte o [referência de erros comuns do ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference).</span><span class="sxs-lookup"><span data-stu-id="41a64-184">See the [ASP.NET Core common errors reference](xref:host-and-deploy/azure-iis-errors-reference).</span></span> <span data-ttu-id="41a64-185">A maioria dos problemas comuns que impedem a inicialização do aplicativo é abordada no tópico de referência.</span><span class="sxs-lookup"><span data-stu-id="41a64-185">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="process-dump-for-a-slow-or-hanging-app"></a><span data-ttu-id="41a64-186">Despejo de processo para um aplicativo lento ou deslocado</span><span class="sxs-lookup"><span data-stu-id="41a64-186">Process dump for a slow or hanging app</span></span>

<span data-ttu-id="41a64-187">Quando um aplicativo responde lentamente ou trava em uma solicitação, consulte [solucionar problemas de desempenho de aplicativo web lenta no serviço de aplicativo do Azure](/azure/app-service/app-service-web-troubleshoot-performance-degradation) para orientação de depuração.</span><span class="sxs-lookup"><span data-stu-id="41a64-187">When an app responds slowly or hangs on a request, see [Troubleshoot slow web app performance issues in Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation) for debugging guidance.</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="41a64-188">Depuração remota</span><span class="sxs-lookup"><span data-stu-id="41a64-188">Remote debugging</span></span>

<span data-ttu-id="41a64-189">Consulte [seção de aplicativos da web de solucionar problemas de um aplicativo web no serviço de aplicativo do Azure usando o Visual Studio de depuração remota](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="41a64-189">See [Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) in the Azure documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="41a64-190">Informações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="41a64-190">Application Insights</span></span>

<span data-ttu-id="41a64-191">[Application Insights](https://azure.microsoft.com/services/application-insights/) fornece a telemetria de aplicativos hospedados no serviço de aplicativo do Azure, incluindo o log de erros e recursos de relatório.</span><span class="sxs-lookup"><span data-stu-id="41a64-191">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="41a64-192">Application Insights só pode relatar erros ocorridos depois que o aplicativo é iniciado quando recursos de log do aplicativo se tornar disponíveis.</span><span class="sxs-lookup"><span data-stu-id="41a64-192">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="41a64-193">Para obter mais informações, consulte [Application Insights para ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="41a64-193">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="41a64-194">Folhas de monitoramentos</span><span class="sxs-lookup"><span data-stu-id="41a64-194">Monitoring blades</span></span>

<span data-ttu-id="41a64-195">Folhas de monitoramentos fornecem uma alternativa experiência para os métodos descritos anteriormente no tópico de solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="41a64-195">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="41a64-196">Essas folhas podem ser usadas para diagnosticar erros 500.</span><span class="sxs-lookup"><span data-stu-id="41a64-196">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="41a64-197">Confirme que as extensões de núcleo do ASP.NET estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="41a64-197">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="41a64-198">Se as extensões não estiverem instaladas, instale-os manualmente:</span><span class="sxs-lookup"><span data-stu-id="41a64-198">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="41a64-199">No **ferramentas de desenvolvimento** seção de folha, selecione o **extensões** folha.</span><span class="sxs-lookup"><span data-stu-id="41a64-199">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="41a64-200">O **ASP.NET Core extensões** devem aparecer na lista.</span><span class="sxs-lookup"><span data-stu-id="41a64-200">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="41a64-201">Se as extensões não estão instaladas, selecione o **adicionar** botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-201">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="41a64-202">Escolha o **ASP.NET Core extensões** da lista.</span><span class="sxs-lookup"><span data-stu-id="41a64-202">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="41a64-203">Selecione **Okey** para aceitar os termos legais.</span><span class="sxs-lookup"><span data-stu-id="41a64-203">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="41a64-204">Selecione **Okey** no **Adicionar extensão** folha.</span><span class="sxs-lookup"><span data-stu-id="41a64-204">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="41a64-205">Uma mensagem pop-up informativa indica quando as extensões são instaladas com êxito.</span><span class="sxs-lookup"><span data-stu-id="41a64-205">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="41a64-206">Se o log de stdout não está habilitado, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="41a64-206">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="41a64-207">No portal do Azure, selecione o **ferramentas avançadas de** folha no **ferramentas de desenvolvimento** área.</span><span class="sxs-lookup"><span data-stu-id="41a64-207">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="41a64-208">Selecione o **vá&rarr;**  botão.</span><span class="sxs-lookup"><span data-stu-id="41a64-208">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="41a64-209">O console do Kudu é aberto em uma janela ou nova guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="41a64-209">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="41a64-210">Usando a barra de navegação na parte superior da página, abra **console de depuração** e selecione **CMD**.</span><span class="sxs-lookup"><span data-stu-id="41a64-210">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="41a64-211">Abra as pastas no caminho **site** > **wwwroot** e role para baixo para revelar o *Web. config* arquivo na parte inferior da lista.</span><span class="sxs-lookup"><span data-stu-id="41a64-211">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="41a64-212">Clique no ícone de lápis ao lado de *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-212">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="41a64-213">Definir **stdoutLogEnabled** para `true` e altere o **stdoutLogFile** caminho: `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="41a64-213">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="41a64-214">Selecione **salvar** para salvar o documento atualizado *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="41a64-214">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="41a64-215">Vá para ativar o log de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="41a64-215">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="41a64-216">No portal do Azure, selecione o **logs de diagnóstico** folha.</span><span class="sxs-lookup"><span data-stu-id="41a64-216">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="41a64-217">Selecione o **na** alternar **(sistema de arquivos) de log de aplicativo** e **mensagens de erro detalhadas**.</span><span class="sxs-lookup"><span data-stu-id="41a64-217">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="41a64-218">Selecione o **salvar** botão na parte superior da folha.</span><span class="sxs-lookup"><span data-stu-id="41a64-218">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="41a64-219">Para incluir o rastreamento de solicitação com falha, também conhecido como registro em log de falha na solicitação evento buffer (FREB), selecione o **na** alternar **o rastreamento de solicitação com falha**.</span><span class="sxs-lookup"><span data-stu-id="41a64-219">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span> 
1. <span data-ttu-id="41a64-220">Selecione o **fluxo de Log** folha, que é listada imediatamente sob o **logs de diagnóstico** folha no portal.</span><span class="sxs-lookup"><span data-stu-id="41a64-220">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="41a64-221">Fazer uma solicitação para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="41a64-221">Make a request to the app.</span></span>
1. <span data-ttu-id="41a64-222">Dentro dos dados de fluxo de log, a causa do erro é indicada.</span><span class="sxs-lookup"><span data-stu-id="41a64-222">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="41a64-223">**Importante!**</span><span class="sxs-lookup"><span data-stu-id="41a64-223">**Important!**</span></span> <span data-ttu-id="41a64-224">Certifique-se de desabilitar stdout registro em log quando o problema for solucionado.</span><span class="sxs-lookup"><span data-stu-id="41a64-224">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="41a64-225">Consulte as instruções de [log do módulo do ASP.NET Core stdout](#aspnet-core-module-stdout-log) seção.</span><span class="sxs-lookup"><span data-stu-id="41a64-225">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="41a64-226">Para exibir os logs de rastreamento de solicitação com falha (logs FREB):</span><span class="sxs-lookup"><span data-stu-id="41a64-226">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="41a64-227">Navegue até o **diagnosticar e resolver problemas** folha no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="41a64-227">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="41a64-228">Selecione **falha os Logs de rastreamento de solicitação** do **ferramentas de suporte** área de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="41a64-228">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="41a64-229">Consulte [seção o habilite o log de diagnóstico para aplicativos web no tópico de serviço de aplicativo do Azure de rastreamentos de solicitação com falha](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) e [perguntas frequentes do desempenho do aplicativo para aplicativos Web no Azure: como ativar o rastreamento de solicitação com falha?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="41a64-229">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="41a64-230">Para obter mais informações, consulte [habilitar o log de diagnóstico para aplicativos web no serviço de aplicativo do Azure](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="41a64-230">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="41a64-231">Falha ao desabilitar o log de stdout pode levar a falhas de aplicativo ou servidor.</span><span class="sxs-lookup"><span data-stu-id="41a64-231">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="41a64-232">Não há nenhum limite no tamanho do arquivo de log ou o número de arquivos de log criados.</span><span class="sxs-lookup"><span data-stu-id="41a64-232">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="41a64-233">Para log de rotina no aplicativo do ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e gira logs.</span><span class="sxs-lookup"><span data-stu-id="41a64-233">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="41a64-234">Para obter mais informações, consulte [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="41a64-234">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41a64-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="41a64-235">Additional resources</span></span>

* [<span data-ttu-id="41a64-236">Introdução ao ASP.NET Core de tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="41a64-236">Introduction to Error Handling in ASP.NET Core</span></span>](xref:fundamentals/error-handling)
* [<span data-ttu-id="41a64-237">Referência de erros comuns para o Serviço de Aplicativo do Azure e o IIS com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41a64-237">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>](xref:host-and-deploy/azure-iis-errors-reference)
* [<span data-ttu-id="41a64-238">Solucionar problemas de um aplicativo web no serviço de aplicativo do Azure usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41a64-238">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="41a64-239">Solucionar problemas de erros HTTP de "502 gateway incorreto" e "503 Serviço indisponível" em seus aplicativos web do Azure</span><span class="sxs-lookup"><span data-stu-id="41a64-239">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="41a64-240">Solucionar problemas de desempenho do aplicativo web lenta no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="41a64-240">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="41a64-241">Perguntas frequentes do desempenho do aplicativo para aplicativos Web no Azure</span><span class="sxs-lookup"><span data-stu-id="41a64-241">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="41a64-242">Azure sexta-feira: Diagnóstico do serviço de aplicativo do Azure e experiência de solução de problemas (vídeo de 12 minutos)</span><span class="sxs-lookup"><span data-stu-id="41a64-242">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)