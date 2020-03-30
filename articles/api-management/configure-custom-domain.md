---
title: Configurare il nome di dominio personalizzato per l'istanza di Gestione API di AzureConfigure custom domain name for Azure API Management instance
titleSuffix: Azure API Management
description: Questo argomento descrive come configurare un nome di dominio personalizzato per l'istanza di Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335843"
---
# <a name="configure-a-custom-domain-name"></a>Configurare un nome di dominio personalizzato

Quando si crea un'istanza del servizio Gestione API `azure-api.net` di Azure, `apim-service-name.azure-api.net`Azure le assegna un sottodominio, ad esempio . Tuttavia, è possibile esporre gli endpoint di Gestione API utilizzando il proprio nome di dominio personalizzato, ad esempio **contoso.com**. Questa esercitazione illustra come eseguire il mapping di un nome DNS personalizzato esistente agli endpoint esposti da un'istanza di Gestione API.

> [!IMPORTANT]
> Gestione API accetta solo le richieste con valori di [intestazione host](https://tools.ietf.org/html/rfc2616#section-14.23) corrispondenti al nome di dominio predefinito o a uno dei nomi di dominio personalizzati configurati.

> [!WARNING]
> I clienti che desiderano utilizzare il blocco dei certificati per migliorare la sicurezza delle applicazioni devono utilizzare un nome di dominio e un certificato personalizzati gestiti, non il certificato predefinito. I clienti che bloccano invece il certificato predefinito avranno una dipendenza rigida dalle proprietà del certificato che non controllano, il che non è una procedura consigliata.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

-   Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
-   Un nome di dominio personalizzato di proprietà dell'utente o dell'organizzazione. In questo argomento non vengono fornite istruzioni su come procurarsi un nome di dominio personalizzato.
-   Un record CNAME ospitato in un server DNS che esegue il mapping del nome di dominio personalizzato al nome di dominio predefinito dell'istanza di Gestione API. In questo argomento non vengono fornite istruzioni su come ospitare un record CNAME.
-   È necessario avere un certificato valido con una chiave pubblica e privata (.PFX). Il nome alternativo del soggetto o del soggetto (SAN) deve corrispondere al nome di dominio (in questo modo l'istanza di Gestione API può esporre in modo sicuro gli URL su TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usare il portale di Azure per impostare un nome di dominio personalizzato

1. Passare all'istanza di Gestione API nel portale di Azure.Navigate to your API Management instance in the [Azure portal](https://portal.azure.com/).
1. Selezionare **Domini personalizzati**.

    Sono disponibili numerosi endpoint a cui è possibile assegnare un nome di dominio personalizzato. Attualmente sono disponibili gli endpoint seguenti:

    - **Gateway** (il `<apim-service-name>.azure-api.net`valore predefinito è: ),
    - **Portale** (il valore predefinito è: `<apim-service-name>.portal.azure-api.net`),
    - **Gestione** (il valore predefinito è: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (il `<apim-service-name>.scm.azure-api.net`valore predefinito è: ),
    - **NewPortal** (il `<apim-service-name>.developer.azure-api.net`valore predefinito è: ).

    > [!NOTE]
    > Solo l'endpoint **Gateway** è disponibile per la configurazione nel livello Consumo.
    > È possibile aggiornare tutti gli endpoint o alcuni di essi. In genere, i clienti aggiornano **Gateway** (questo URL viene utilizzato per chiamare l'API esposta tramite Gestione API) e **Portale** (l'URL del portale per sviluppatori).
    > **Gli** endpoint di gestione e **SCM** vengono utilizzati internamente solo dai proprietari delle istanze di Gestione API e pertanto meno frequentemente viene assegnato un nome di dominio personalizzato.
    > Il livello **Premium** supporta l'impostazione di più nomi host per l'endpoint **gateway.**

1. Selezionare l'endpoint che si vuole aggiornare.
1. Nella finestra di destra fare clic su **Personalizzato**.

    - In **Nome di dominio personalizzato** specificare il nome che si vuole usare. Ad esempio: `api.contoso.com`.
    - Nel **certificato**, selezionare un certificato da Key Vault. È inoltre possibile caricare un file . PFX e fornire la relativa **Password**, se il certificato è protetto con una password.

    > [!NOTE]
    > I nomi di dominio `*.contoso.com` con caratteri jolly, ad esempio, sono supportati in tutti i livelli ad eccezione del livello Consumo.

    > [!TIP]
    > È consigliabile usare L'insieme di credenziali delle chiavi di Azure per la gestione dei certificati e impostarli per la rotazione automatica.
    > Se si usa l'insieme di credenziali delle chiavi di Azure per gestire il certificato TLS/SSL del dominio personalizzato, assicurarsi che il certificato sia inserito nell'insieme di credenziali delle chiavi [come _certificato,_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)non come _segreto._
    >
    > Per recuperare un certificato TLS/SSL, Gestione API deve disporre dell'elenco e ottenere autorizzazioni per i segreti nell'insieme di credenziali delle chiavi di Azure contenente il certificato. Quando si usa il portale di Azure, tutti i passaggi di configurazione necessari verranno completati automaticamente. Quando si usano gli strumenti della riga di comando o l'API di gestione, queste autorizzazioni devono essere concesse manualmente. Questa operazione si esegue in due passaggi. In primo luogo, utilizzare la pagina Identità gestite nell'istanza di Gestione API per assicurarsi che l'identità gestita sia abilitata e prendere nota dell'ID principale visualizzato in tale pagina. In secondo luogo, assegnare l'elenco delle autorizzazioni e ottenere le autorizzazioni per i segreti per questo ID principale nell'insieme di credenziali delle chiavi di Azure contenente il certificato.
    >
    > Se il certificato è impostato per il rotolo automatico, Gestione API rileverà automaticamente la versione più recente senza tempi di inattività per il servizio (se il livello Gestione API ha SLA - i. e in tutti i livelli tranne il livello Sviluppatore).

1. Fare clic su Applica.

    > [!NOTE]
    > Il processo di assegnazione del certificato potrebbe richiedere circa 15 minuti o più, a seconda delle dimensioni della distribuzione. Gli SKU per sviluppatori hanno tempi di inattività, gli SKU Basic e superiori non hanno tempi di inattività.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configurazione del DNS

Quando si configura DNS per il nome di dominio personalizzato, sono disponibili due opzioni:

-   Configurare un record CNAME che punti all'endpoint del nome di dominio personalizzato configurato.
-   Configurare un Record A che punti all'indirizzo IP del gateway di Gestione API.

> [!NOTE]
> Anche se l'indirizzo IP dell'istanza di Managment API è statico, può cambiare in alcuni scenari. Per questo motivo è consigliabile utilizzare CNAME durante la configurazione del dominio personalizzato. Prendere in considerazione questo quando si sceglie il metodo di configurazione DNS. Per ulteriori [informazioni, vedere l'articolo](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) sulla documentazione IP e le [domande frequenti su Gestione API](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare il servizio](upgrade-and-scale.md)
