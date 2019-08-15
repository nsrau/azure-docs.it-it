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
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 45e1ad6bd757ec5acaf784c94e4cfb5e487ce9ba
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975744"
---
# <a name="configure-a-custom-domain-name"></a>Configurare un nome di dominio personalizzato

Quando si crea un'istanza del servizio gestione API di Azure, Azure lo assegna a un sottodominio di azure-api.net (ad `apim-service-name.azure-api.net`esempio,). Tuttavia, è possibile esporre gli endpoint di gestione API usando il nome di dominio personalizzato, ad esempio **contoso.com**. Questa esercitazione illustra come eseguire il mapping di un nome DNS personalizzato esistente agli endpoint esposti da un'istanza di gestione API.

> [!WARNING]
> I clienti che vogliono usare l'associazione del certificato per migliorare la sicurezza delle applicazioni devono usare un nome di dominio personalizzato e il certificato che gestiscono, non il certificato predefinito. I clienti che usano invece il certificato predefinito dipenderanno in modo sostanziale dalle proprietà di un certificato che non controllano, adottando così una procedura non consigliata.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

-   Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Istanza di gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
-   Nome di dominio personalizzato posseduto dall'utente o dall'organizzazione. In questo argomento non vengono fornite istruzioni su come ottenere un nome di dominio personalizzato.
-   Record CNAME ospitato in un server DNS che esegue il mapping del nome di dominio personalizzato al nome di dominio predefinito dell'istanza di gestione API. In questo argomento non vengono fornite istruzioni su come ospitare un record CNAME.
-   È necessario avere un certificato valido con una chiave pubblica e privata (.PFX). L'oggetto o il nome alternativo del soggetto (SAN) deve corrispondere al nome di dominio (questo consente all'istanza di gestione API di esporre in modo sicuro gli URL tramite SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usare il portale di Azure per impostare un nome di dominio personalizzato

1. Passare all'istanza di gestione API nel [portale di Azure](https://portal.azure.com/).
1. Selezionare **domini personalizzati**.

    Sono disponibili diversi endpoint a cui è possibile assegnare un nome di dominio personalizzato. Attualmente sono disponibili gli endpoint seguenti:

    - **Gateway** (il valore predefinito `<apim-service-name>.azure-api.net`è:),
    - **Portale** (il valore predefinito è: `<apim-service-name>.portal.azure-api.net`),
    - **Gestione** (il valore predefinito è: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (il valore predefinito è: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Solo l'endpoint del **gateway** disponibile per la configurazione nel livello di consumo.
    > È possibile aggiornare tutti gli endpoint o alcuni di essi. In genere, i clienti aggiornano il **gateway** (questo URL viene usato per chiamare l'API esposta tramite gestione API) e il **portale** (l'URL del portale per sviluppatori).
    > Gli endpoint di **gestione** e **SCM** vengono usati internamente dai proprietari dell'istanza di gestione API e pertanto vengono assegnati con minore frequenza a un nome di dominio personalizzato.
    > Il livello **Premium** supporta l'impostazione di più nomi host per l'endpoint del **gateway** .

1. Selezionare l'endpoint che si vuole aggiornare.
1. Nella finestra di destra fare clic su **Personalizzato**.

    - In **Nome di dominio personalizzato** specificare il nome che si vuole usare. Ad esempio `api.contoso.com`.
    - Nel **certificato**selezionare un certificato da Key Vault. È anche possibile caricare un valido. File PFX e fornire la relativa **password**, se il certificato è protetto con una password.

    > [!NOTE]
    > I nomi di dominio con caratteri `*.contoso.com` jolly, ad esempio, sono supportati in tutti i livelli eccetto il livello di consumo.

    > [!TIP]
    > Si consiglia di usare Azure Key Vault per gestire i certificati e impostarli per la rotazione.
    > Se si usa Azure Key Vault per gestire il certificato SSL del dominio personalizzato, assicurarsi che il certificato venga inserito in Key Vault [come _certificato_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), non come _segreto_.
    >
    > Per recuperare un certificato SSL, gestione API deve avere l'elenco delle autorizzazioni Get Secrets sul Azure Key Vault contenente il certificato. Quando si usa portale di Azure tutti i passaggi di configurazione necessari verranno completati automaticamente. Quando si usano gli strumenti da riga di comando o l'API di gestione, è necessario concedere queste autorizzazioni manualmente. Questa operazione si esegue in due passaggi. Per prima cosa, usare la pagina identità gestite nell'istanza di gestione API per assicurarsi che l'identità gestita sia abilitata e prendere nota dell'ID entità visualizzato nella pagina. In secondo luogo, concedere l'elenco di autorizzazioni e ottenere le autorizzazioni Secrets per questo ID entità nel Azure Key Vault contenente il certificato.
    >
    > Se il certificato è impostato per la rotazione automatica, gestione API rileverà automaticamente la versione più recente senza tempi di inattività per il servizio (se il livello di gestione API ha SLA-i. e. in tutti i livelli eccetto il livello Developer).

1. Fare clic su Applica.

    > [!NOTE]
    > Il processo di assegnazione del certificato potrebbe richiedere circa 15 minuti o più, a seconda delle dimensioni della distribuzione. Lo SKU Developer presenta tempi di inattività, gli SKU Basic e superiori non hanno tempi di inattività.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configurazione del DNS

Quando si configura DNS per il nome di dominio personalizzato, sono disponibili due opzioni:

-   Configurare un record CNAME che punta all'endpoint del nome di dominio personalizzato configurato.
-   Configurare un record A che punti all'indirizzo IP del gateway di gestione API.

> [!NOTE]
> Anche se l'indirizzo IP dell'istanza di gestione API è statico, può cambiare in alcuni scenari. Per questo motivo è consigliabile usare CNAME durante la configurazione di un dominio personalizzato. Prendere in considerazione questo aspetto quando si sceglie il metodo di configurazione DNS. Per altre informazioni, vedere le [domande frequenti sull'API Mananagement](https://docs.microsoft.com/azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules).

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare il servizio](upgrade-and-scale.md)
