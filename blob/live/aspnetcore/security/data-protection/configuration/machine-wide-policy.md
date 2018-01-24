---
title: "Suporte a política de máquina de proteção de dados no ASP.NET Core"
author: rick-anderson
description: "Saiba mais sobre suporte para definição de uma política de todo o computador padrão para todos os aplicativos que consomem a proteção de dados do ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 4c3ae3b628ebe17c7926c71f1fad664d719d1706
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a><span data-ttu-id="b6edc-103">Suporte a política de máquina de proteção de dados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6edc-103">Data Protection machine-wide policy support in ASP.NET Core</span></span>

<span data-ttu-id="b6edc-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b6edc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b6edc-105">Quando em execução no Windows, o sistema de proteção de dados tem suporte limitado para definir uma política de todo o computador padrão para todos os aplicativos que consomem a proteção de dados do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6edc-105">When running on Windows, the Data Protection system has limited support for setting a default machine-wide policy for all apps that consume ASP.NET Core Data Protection.</span></span> <span data-ttu-id="b6edc-106">A ideia geral é que um administrador pode querer alterar uma configuração padrão, como os algoritmos usados ou a vida útil da chave, sem a necessidade de atualizar manualmente cada aplicativo no computador.</span><span class="sxs-lookup"><span data-stu-id="b6edc-106">The general idea is that an administrator might wish to change a default setting, such as the algorithms used or key lifetime, without the need to manually update every app on the machine.</span></span>

> [!WARNING]
> <span data-ttu-id="b6edc-107">O administrador do sistema pode definir a política padrão, mas eles não é possível impor a ele.</span><span class="sxs-lookup"><span data-stu-id="b6edc-107">The system administrator can set default policy, but they can't enforce it.</span></span> <span data-ttu-id="b6edc-108">O desenvolvedor do aplicativo sempre pode substituir qualquer valor com um dos seus próprios escolhendo.</span><span class="sxs-lookup"><span data-stu-id="b6edc-108">The app developer can always override any value with one of their own choosing.</span></span> <span data-ttu-id="b6edc-109">A política padrão afeta somente os aplicativos em que o desenvolvedor não foi especificado um valor explícito para uma configuração.</span><span class="sxs-lookup"><span data-stu-id="b6edc-109">The default policy only affects apps where the developer hasn't specified an explicit value for a setting.</span></span>

## <a name="setting-default-policy"></a><span data-ttu-id="b6edc-110">Configuração de política padrão</span><span class="sxs-lookup"><span data-stu-id="b6edc-110">Setting default policy</span></span>

<span data-ttu-id="b6edc-111">Para definir a política padrão, um administrador pode definir os valores conhecidos no registro do sistema na seguinte chave do registro:</span><span class="sxs-lookup"><span data-stu-id="b6edc-111">To set default policy, an administrator can set known values in the system registry under the following registry key:</span></span>

<span data-ttu-id="b6edc-112">**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**</span><span class="sxs-lookup"><span data-stu-id="b6edc-112">**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**</span></span>

<span data-ttu-id="b6edc-113">Se você estiver em um sistema operacional de 64 bits e quiser afetar o comportamento de aplicativos de 32 bits, lembre-se de configurar o equivalente Wow6432Node a chave acima.</span><span class="sxs-lookup"><span data-stu-id="b6edc-113">If you're on a 64-bit operating system and want to affect the behavior of 32-bit apps, remember to configure the Wow6432Node equivalent of the above key.</span></span>

<span data-ttu-id="b6edc-114">Os valores com suporte são mostrados abaixo.</span><span class="sxs-lookup"><span data-stu-id="b6edc-114">The supported values are shown below.</span></span>

| <span data-ttu-id="b6edc-115">Valor</span><span class="sxs-lookup"><span data-stu-id="b6edc-115">Value</span></span>              | <span data-ttu-id="b6edc-116">Tipo</span><span class="sxs-lookup"><span data-stu-id="b6edc-116">Type</span></span>   | <span data-ttu-id="b6edc-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6edc-117">Description</span></span> |
| ------------------ | :----: | ----------- |
| <span data-ttu-id="b6edc-118">EncryptionType</span><span class="sxs-lookup"><span data-stu-id="b6edc-118">EncryptionType</span></span>     | <span data-ttu-id="b6edc-119">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-119">string</span></span> | <span data-ttu-id="b6edc-120">Especifica os algoritmos que devem ser usados para proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="b6edc-120">Specifies which algorithms should be used for data protection.</span></span> <span data-ttu-id="b6edc-121">O valor deve ser CBC CNG, GCM CNG ou gerenciado e é descrito com mais detalhes abaixo.</span><span class="sxs-lookup"><span data-stu-id="b6edc-121">The value must be CNG-CBC, CNG-GCM, or Managed and is described in more detail below.</span></span> |
| <span data-ttu-id="b6edc-122">DefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="b6edc-122">DefaultKeyLifetime</span></span> | <span data-ttu-id="b6edc-123">DWORD</span><span class="sxs-lookup"><span data-stu-id="b6edc-123">DWORD</span></span>  | <span data-ttu-id="b6edc-124">Especifica o tempo de vida para chaves geradas recentemente.</span><span class="sxs-lookup"><span data-stu-id="b6edc-124">Specifies the lifetime for newly-generated keys.</span></span> <span data-ttu-id="b6edc-125">O valor é especificado em dias e deve ser > = 7.</span><span class="sxs-lookup"><span data-stu-id="b6edc-125">The value is specified in days and must be >= 7.</span></span> |
| <span data-ttu-id="b6edc-126">KeyEscrowSinks</span><span class="sxs-lookup"><span data-stu-id="b6edc-126">KeyEscrowSinks</span></span>     | <span data-ttu-id="b6edc-127">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-127">string</span></span> | <span data-ttu-id="b6edc-128">Especifica os tipos que são usados para caução de chaves.</span><span class="sxs-lookup"><span data-stu-id="b6edc-128">Specifies the types that are used for key escrow.</span></span> <span data-ttu-id="b6edc-129">O valor é uma lista separada por ponto-e-vírgula de Coletores de caução de chaves, onde cada elemento na lista é o nome qualificado do assembly de um tipo que implementa [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink).</span><span class="sxs-lookup"><span data-stu-id="b6edc-129">The value is a semicolon-delimited list of key escrow sinks, where each element in the list is the assembly-qualified name of a type that implements [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink).</span></span> |

## <a name="encryption-types"></a><span data-ttu-id="b6edc-130">Tipos de criptografia</span><span class="sxs-lookup"><span data-stu-id="b6edc-130">Encryption types</span></span>

<span data-ttu-id="b6edc-131">Se EncryptionType é CBC CNG, o sistema está configurado para usar uma codificação de bloco simétrica de modo CBC para confidencialidade e HMAC autenticidade com serviços fornecidos pelo Windows CNG (consulte [especificando algoritmos personalizados de Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) para mais detalhes).</span><span class="sxs-lookup"><span data-stu-id="b6edc-131">If EncryptionType is CNG-CBC, the system is configured to use a CBC-mode symmetric block cipher for confidentiality and HMAC for authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) for more details).</span></span> <span data-ttu-id="b6edc-132">Os seguintes valores adicionais são suportados, cada uma correspondendo a uma propriedade do tipo CngCbcAuthenticatedEncryptionSettings.</span><span class="sxs-lookup"><span data-stu-id="b6edc-132">The following additional values are supported, each of which corresponds to a property on the CngCbcAuthenticatedEncryptionSettings type.</span></span>

| <span data-ttu-id="b6edc-133">Valor</span><span class="sxs-lookup"><span data-stu-id="b6edc-133">Value</span></span>                       | <span data-ttu-id="b6edc-134">Tipo</span><span class="sxs-lookup"><span data-stu-id="b6edc-134">Type</span></span>   | <span data-ttu-id="b6edc-135">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6edc-135">Description</span></span> |
| --------------------------- | :----: | ----------- |
| <span data-ttu-id="b6edc-136">EncryptionAlgorithm</span><span class="sxs-lookup"><span data-stu-id="b6edc-136">EncryptionAlgorithm</span></span>         | <span data-ttu-id="b6edc-137">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-137">string</span></span> | <span data-ttu-id="b6edc-138">O nome de um algoritmo de criptografia simétrica bloco entendido pelo CNG.</span><span class="sxs-lookup"><span data-stu-id="b6edc-138">The name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="b6edc-139">Esse algoritmo é aberto no modo CBC.</span><span class="sxs-lookup"><span data-stu-id="b6edc-139">This algorithm is opened in CBC mode.</span></span> |
| <span data-ttu-id="b6edc-140">EncryptionAlgorithmProvider</span><span class="sxs-lookup"><span data-stu-id="b6edc-140">EncryptionAlgorithmProvider</span></span> | <span data-ttu-id="b6edc-141">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-141">string</span></span> | <span data-ttu-id="b6edc-142">O nome da implementação do provedor CNG que pode produzir o algoritmo EncryptionAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="b6edc-142">The name of the CNG provider implementation that can produce the algorithm EncryptionAlgorithm.</span></span> |
| <span data-ttu-id="b6edc-143">EncryptionAlgorithmKeySize</span><span class="sxs-lookup"><span data-stu-id="b6edc-143">EncryptionAlgorithmKeySize</span></span>  | <span data-ttu-id="b6edc-144">DWORD</span><span class="sxs-lookup"><span data-stu-id="b6edc-144">DWORD</span></span>  | <span data-ttu-id="b6edc-145">O comprimento (em bits) da chave derivada para o algoritmo de criptografia simétrica de bloco.</span><span class="sxs-lookup"><span data-stu-id="b6edc-145">The length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span> |
| <span data-ttu-id="b6edc-146">HashAlgorithm</span><span class="sxs-lookup"><span data-stu-id="b6edc-146">HashAlgorithm</span></span>               | <span data-ttu-id="b6edc-147">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-147">string</span></span> | <span data-ttu-id="b6edc-148">O nome de um algoritmo de hash entendido pelo CNG.</span><span class="sxs-lookup"><span data-stu-id="b6edc-148">The name of a hash algorithm understood by CNG.</span></span> <span data-ttu-id="b6edc-149">Esse algoritmo é aberto no modo HMAC.</span><span class="sxs-lookup"><span data-stu-id="b6edc-149">This algorithm is opened in HMAC mode.</span></span> |
| <span data-ttu-id="b6edc-150">HashAlgorithmProvider</span><span class="sxs-lookup"><span data-stu-id="b6edc-150">HashAlgorithmProvider</span></span>       | <span data-ttu-id="b6edc-151">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-151">string</span></span> | <span data-ttu-id="b6edc-152">O nome da implementação do provedor CNG que pode produzir o algoritmo HashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="b6edc-152">The name of the CNG provider implementation that can produce the algorithm HashAlgorithm.</span></span> |

<span data-ttu-id="b6edc-153">Se EncryptionType é GCM CNG, o sistema está configurado para usar uma codificação de bloco simétrica de modo Galois/contador para autenticidade e confidencialidade com serviços fornecidos pelo Windows CNG (consulte [especificando algoritmos personalizados de Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) Para obter mais detalhes).</span><span class="sxs-lookup"><span data-stu-id="b6edc-153">If EncryptionType is CNG-GCM, the system is configured to use a Galois/Counter Mode symmetric block cipher for confidentiality and authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) for more details).</span></span> <span data-ttu-id="b6edc-154">Os seguintes valores adicionais são suportados, cada uma correspondendo a uma propriedade do tipo CngGcmAuthenticatedEncryptionSettings.</span><span class="sxs-lookup"><span data-stu-id="b6edc-154">The following additional values are supported, each of which corresponds to a property on the CngGcmAuthenticatedEncryptionSettings type.</span></span>

| <span data-ttu-id="b6edc-155">Valor</span><span class="sxs-lookup"><span data-stu-id="b6edc-155">Value</span></span>                       | <span data-ttu-id="b6edc-156">Tipo</span><span class="sxs-lookup"><span data-stu-id="b6edc-156">Type</span></span>   | <span data-ttu-id="b6edc-157">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6edc-157">Description</span></span> |
| --------------------------- | :----: | ----------- |
| <span data-ttu-id="b6edc-158">EncryptionAlgorithm</span><span class="sxs-lookup"><span data-stu-id="b6edc-158">EncryptionAlgorithm</span></span>         | <span data-ttu-id="b6edc-159">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-159">string</span></span> | <span data-ttu-id="b6edc-160">O nome de um algoritmo de criptografia simétrica bloco entendido pelo CNG.</span><span class="sxs-lookup"><span data-stu-id="b6edc-160">The name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="b6edc-161">Esse algoritmo é aberto no modo de Galois/contador.</span><span class="sxs-lookup"><span data-stu-id="b6edc-161">This algorithm is opened in Galois/Counter Mode.</span></span> |
| <span data-ttu-id="b6edc-162">EncryptionAlgorithmProvider</span><span class="sxs-lookup"><span data-stu-id="b6edc-162">EncryptionAlgorithmProvider</span></span> | <span data-ttu-id="b6edc-163">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-163">string</span></span> | <span data-ttu-id="b6edc-164">O nome da implementação do provedor CNG que pode produzir o algoritmo EncryptionAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="b6edc-164">The name of the CNG provider implementation that can produce the algorithm EncryptionAlgorithm.</span></span> |
| <span data-ttu-id="b6edc-165">EncryptionAlgorithmKeySize</span><span class="sxs-lookup"><span data-stu-id="b6edc-165">EncryptionAlgorithmKeySize</span></span>  | <span data-ttu-id="b6edc-166">DWORD</span><span class="sxs-lookup"><span data-stu-id="b6edc-166">DWORD</span></span>  | <span data-ttu-id="b6edc-167">O comprimento (em bits) da chave derivada para o algoritmo de criptografia simétrica de bloco.</span><span class="sxs-lookup"><span data-stu-id="b6edc-167">The length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span> |

<span data-ttu-id="b6edc-168">Se EncryptionType for gerenciado, o sistema está configurado para usar um SymmetricAlgorithm gerenciado para confidencialidade e KeyedHashAlgorithm autenticidade (consulte [especificando personalizado gerenciado algoritmos](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) para obter mais detalhes).</span><span class="sxs-lookup"><span data-stu-id="b6edc-168">If EncryptionType is Managed, the system is configured to use a managed SymmetricAlgorithm for confidentiality and KeyedHashAlgorithm for authenticity (see [Specifying custom managed algorithms](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) for more details).</span></span> <span data-ttu-id="b6edc-169">Os seguintes valores adicionais são suportados, cada uma correspondendo a uma propriedade do tipo ManagedAuthenticatedEncryptionSettings.</span><span class="sxs-lookup"><span data-stu-id="b6edc-169">The following additional values are supported, each of which corresponds to a property on the ManagedAuthenticatedEncryptionSettings type.</span></span>

| <span data-ttu-id="b6edc-170">Valor</span><span class="sxs-lookup"><span data-stu-id="b6edc-170">Value</span></span>                      | <span data-ttu-id="b6edc-171">Tipo</span><span class="sxs-lookup"><span data-stu-id="b6edc-171">Type</span></span>   | <span data-ttu-id="b6edc-172">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6edc-172">Description</span></span> |
| -------------------------- | :----: | ----------- |
| <span data-ttu-id="b6edc-173">EncryptionAlgorithmType</span><span class="sxs-lookup"><span data-stu-id="b6edc-173">EncryptionAlgorithmType</span></span>    | <span data-ttu-id="b6edc-174">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-174">string</span></span> | <span data-ttu-id="b6edc-175">O nome qualificado do assembly de um tipo que implementa SymmetricAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="b6edc-175">The assembly-qualified name of a type that implements SymmetricAlgorithm.</span></span> |
| <span data-ttu-id="b6edc-176">EncryptionAlgorithmKeySize</span><span class="sxs-lookup"><span data-stu-id="b6edc-176">EncryptionAlgorithmKeySize</span></span> | <span data-ttu-id="b6edc-177">DWORD</span><span class="sxs-lookup"><span data-stu-id="b6edc-177">DWORD</span></span>  | <span data-ttu-id="b6edc-178">O comprimento (em bits) da chave para derivar o algoritmo de criptografia simétrica.</span><span class="sxs-lookup"><span data-stu-id="b6edc-178">The length (in bits) of the key to derive for the symmetric encryption algorithm.</span></span> |
| <span data-ttu-id="b6edc-179">ValidationAlgorithmType</span><span class="sxs-lookup"><span data-stu-id="b6edc-179">ValidationAlgorithmType</span></span>    | <span data-ttu-id="b6edc-180">cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="b6edc-180">string</span></span> | <span data-ttu-id="b6edc-181">O nome qualificado do assembly de um tipo que implementa KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="b6edc-181">The assembly-qualified name of a type that implements KeyedHashAlgorithm.</span></span> |

<span data-ttu-id="b6edc-182">Se EncryptionType tiver qualquer valor diferente de nulo ou vazio, o sistema de proteção de dados gera uma exceção durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="b6edc-182">If EncryptionType has any other value other than null or empty, the Data Protection system throws an exception at startup.</span></span>

> [!WARNING]
> <span data-ttu-id="b6edc-183">Ao configurar uma configuração de política padrão que envolve os nomes de tipo (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), os tipos devem estar disponíveis para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6edc-183">When configuring a default policy setting that involves type names (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), the types must be available to the app.</span></span> <span data-ttu-id="b6edc-184">Isso significa que para aplicativos em execução no CLR de área de trabalho, os assemblies que contêm esses tipos devem estar presentes no Cache de Assembly Global (GAC).</span><span class="sxs-lookup"><span data-stu-id="b6edc-184">This means that for apps running on Desktop CLR, the assemblies that contain these types should be present in the Global Assembly Cache (GAC).</span></span> <span data-ttu-id="b6edc-185">Para aplicativos do ASP.NET Core em execução no [.NET Core](https://www.microsoft.com/net/core), os pacotes que contêm esses tipos devem ser instalados.</span><span class="sxs-lookup"><span data-stu-id="b6edc-185">For ASP.NET Core apps running on [.NET Core](https://www.microsoft.com/net/core), the packages that contain these types should be installed.</span></span>