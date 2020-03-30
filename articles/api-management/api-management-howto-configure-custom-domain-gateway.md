---
title: Configurare un nome di dominio personalizzato per il gateway di gestione delle API di Azure self-hosted. Documenti Microsoft
description: Questo argomento descrive i passaggi per la configurazione di un nome di dominio personalizzato per il gateway di Gestione API di Azure self-hosted.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335943"
---
# <a name="configure-a-custom-domain-name"></a>Configurare un nome di dominio personalizzato

Quando si esegue il provisioning di un gateway di Gestione API di [Azure self-hosted,](self-hosted-gateway-overview.md) non viene assegnato il nome host e deve essere fatto riferimento tramite il relativo indirizzo IP. In questo articolo viene illustrato come eseguire il mapping di un nome DNS personalizzato esistente (noto anche come nome host) di un gateway indipendente.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello sviluppatore è limitato a una singola distribuzione di gateway self-hosted.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

-   Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
- Un gateway self-hosted. Per altre informazioni, vedere Come eseguire il provisioning del [gateway self-hostedFor more information, see How to provision self-hosted gateway](api-management-howto-provision-self-hosted-gateway.md)
-   Un nome di dominio personalizzato di proprietà dell'utente o dell'organizzazione. In questo argomento non vengono fornite istruzioni su come procurarsi un nome di dominio personalizzato.
-   Record DNS ospitato su un server DNS che esegue il mapping del nome di dominio personalizzato all'indirizzo IP del gateway self-hosted. In questo argomento non vengono fornite istruzioni su come ospitare un record DNS.
-   È necessario avere un certificato valido con una chiave pubblica e privata (.PFX). Il nome alternativo del soggetto o del soggetto (SAN) deve corrispondere al nome di dominio (in questo modo l'istanza di Gestione API può esporre in modo sicuro gli URL su TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Aggiungere un certificato di dominio personalizzato al servizio Gestione APIAdd custom domain certificate to your API Management service

1. Selezionare **Certificati** in **Sicurezza**.
2. Selezionare **+ Aggiungi**.
3. Immettere un nome di risorsa per il certificato nel campo **Id.**
4. Selezionare il file contenente il certificato (. PFX) selezionando il campo **Certificato** o l'icona della cartella accanto ad esso.
5. Immettere la password per il certificato nel campo **Password.**
6. Selezionare **Crea** per aggiungere il certificato al servizio Gestione API.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Usare il portale di Azure per impostare un nome di dominio personalizzato per il gateway indipendenteUse the Azure portal to set a custom domain name for your self-hosted gateway

1. Selezionare **I gateway** in **Impostazioni**.
2. Selezionare il gateway self-hosted per il quale si desidera configurare il nome di dominio.
3. Selezionare **Nomi host** in **Impostazioni**.
4. Selezionare **: Aggiungi**
5. Immettere il nome della risorsa per il nome host nel campo **Nome.**
6. Immettere il nome di dominio nel campo **Nome host.**
7. Selezionare un certificato dall'elenco a discesa **Certificato.Select** a certificate from the Certificate dropdown.
8. Selezionare la casella di controllo **Negozia certificato client** se una delle API esposte tramite questo gateway utilizza l'autenticazione del certificato client.
    > [!WARNING]
    > Questa impostazione è condivisa da tutti i nomi di dominio configurati per il gateway.
9. Selezionare **Aggiungi** per assegnare il nome di dominio personalizzato al gateway self-hosted selezionato.

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare il servizio](upgrade-and-scale.md)
