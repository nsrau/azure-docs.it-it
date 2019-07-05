---
title: Configurare un nome di dominio personalizzato per l'istanza di Gestione API di Azure | Microsoft Docs
description: Questo argomento descrive come configurare un nome di dominio personalizzato per l'istanza di Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509305"
---
# <a name="configure-a-custom-domain-name"></a>Configurare un nome di dominio personalizzato

Quando si crea un'istanza del servizio Gestione API di Azure, Azure lo assegna a un sottodominio di Azure-API.NET (ad esempio, `apim-service-name.azure-api.net`). È tuttavia possibile esporre gli endpoint di gestione API usando il proprio nome di dominio personalizzato, ad esempio **contoso.com**. Questa esercitazione illustra come eseguire il mapping di un nome DNS personalizzato esistente agli endpoint esposti da un'istanza di gestione API.

> [!WARNING]
> I clienti che vogliono usare l'associazione del certificato per migliorare la sicurezza delle applicazioni devono usare un nome di dominio personalizzato e il certificato che gestiscono, non il certificato predefinito. I clienti che usano invece il certificato predefinito dipenderanno in modo sostanziale dalle proprietà di un certificato che non controllano, adottando così una procedura non consigliata.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

-   Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Un'istanza di gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
-   Un nome di dominio personalizzato di proprietà dell'utente. Il nome di dominio personalizzato che si vuole usare deve essere acquistato separatamente e ospitato in un server DNS. Questo argomento fornisce istruzioni su come ospitare un nome di dominio personalizzato.
-   È necessario avere un certificato valido con una chiave pubblica e privata (.PFX). Soggetto o nome alternativo del soggetto (SAN) deve corrispondere al nome di dominio (in questo modo l'istanza di gestione API di esporre in modo sicuro gli URL tramite SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usare il portale di Azure per impostare un nome di dominio personalizzato

1. Passare all'istanza di gestione API nel [portale di Azure](https://portal.azure.com/).
1. Selezionare **Domini e SSL personalizzati**.

    Sono presenti un numero di endpoint a cui è possibile assegnare un nome di dominio personalizzato. Attualmente sono disponibili gli endpoint seguenti:

    - **Proxy** (il valore predefinito è: `<apim-service-name>.azure-api.net`),
    - **Portale** (il valore predefinito è: `<apim-service-name>.portal.azure-api.net`),
    - **Gestione** (il valore predefinito è: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (il valore predefinito è: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > È possibile aggiornare tutti gli endpoint o alcuni di essi. In genere, i clienti aggiornano **Proxy** (questo URL viene usato per chiamare l'API esposta tramite Gestione API) e **Portale** (l'URL del portale per sviluppatori). **Gestione** e **SCM** gli endpoint vengono usati internamente da solo i proprietari di istanza di gestione API e pertanto meno frequentemente vengono assegnati un nome di dominio personalizzato. Nella maggior parte dei casi è possibile impostare solo il nome di un singolo dominio per un determinato endpoint. Tuttavia, il **Premium** livello supporta l'impostazione di più nomi host per il **Proxy** endpoint.

1. Selezionare l'endpoint che si vuole aggiornare.
1. Nella finestra di destra fare clic su **Personalizzato**.

    - In **Nome di dominio personalizzato** specificare il nome che si vuole usare. Ad esempio: `api.contoso.com`. I nomi di dominio con caratteri jolly (ad esempio, \*. dominio.com) sono inoltre supportati.
    - Nel **certificato**, selezionare un certificato di Key Vault. È anche possibile caricare un valore valido. File PFX del file e fornire relativi **Password**, se il certificato è protetto con una password.

    > [!TIP]
    > È consigliabile usare Azure Key Vault per la gestione dei certificati e impostandole su autorotate.
    > Se si usa Azure Key Vault per gestire il certificato SSL di dominio personalizzato, assicurarsi che il certificato viene inserito in Key Vault [come un _certificato_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), non una _segreto_.
    >
    > Per recuperare un certificato SSL, gestione API deve avere l'elenco un get segreti autorizzazioni in Azure Key Vault contenente il certificato. Quando si usa il portale di Azure verranno completati automaticamente tutti i passaggi di configurazione necessarie. Quando si usano gli strumenti da riga di comando o API di gestione, queste autorizzazioni devono essere concessi manualmente. Questa operazione si esegue in due passaggi. In primo luogo, è possibile usare pagina identità gestita nell'istanza di gestione API per assicurarsi che vengono abilitate le identità gestita e prendere nota dell'id dell'entità visualizzati nella pagina. In secondo luogo, assegnare l'autorizzazione elenco e ottenere le autorizzazioni di segreti per l'id dell'entità in Azure Key Vault contenente il certificato.
    >
    > Se il certificato è impostato su autorotate, gestione API selezionerà la versione più recente automaticamente senza alcun tempo di inattività per il servizio (se il livello di gestione API con contratto di servizio, vale a dire in tutti i livelli, ad eccezione del livello Developer).

1. Fare clic su Applica.

    > [!NOTE]
    > Il processo di assegnazione del certificato potrebbe richiedere circa 15 minuti o più, a seconda delle dimensioni della distribuzione. Developer SKU ha tempi di inattività, mentre SKU Basic e superiore non ne ha.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare il servizio](upgrade-and-scale.md)
