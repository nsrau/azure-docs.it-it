---
title: Configurare un nome di dominio personalizzato per il gateway di gestione API di Azure self-hosted | Microsoft Docs
description: Questo argomento descrive i passaggi per la configurazione di un nome di dominio personalizzato per il gateway di gestione API di Azure self-hosted.
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
ms.openlocfilehash: 1df2cce04021c1cd14c356311df921dd1c0298e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513809"
---
# <a name="configure-a-custom-domain-name"></a>Configurare un nome di dominio personalizzato

Quando si esegue il provisioning di un [gateway di gestione API di Azure indipendente](self-hosted-gateway-overview.md) , non viene assegnato il nome host ed è necessario fare riferimento al relativo indirizzo IP. Questo articolo illustra come eseguire il mapping di un nome DNS personalizzato esistente (noto anche come nome host) a un gateway self-hosted.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello Developer è limitato a una singola distribuzione del gateway self-hosted.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

-   Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Istanza di gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
- Un gateway self-hosted. Per ulteriori informazioni, vedere [come eseguire il provisioning di un gateway self-hosted](api-management-howto-provision-self-hosted-gateway.md)
-   Nome di dominio personalizzato posseduto dall'utente o dall'organizzazione. In questo argomento non vengono fornite istruzioni su come ottenere un nome di dominio personalizzato.
-   Un record DNS ospitato in un server DNS che esegue il mapping del nome di dominio personalizzato all'indirizzo IP del gateway self-hosted. In questo argomento non vengono fornite istruzioni su come ospitare un record DNS.
-   È necessario avere un certificato valido con una chiave pubblica e privata (.PFX). L'oggetto o il nome alternativo del soggetto (SAN) deve corrispondere al nome di dominio (questo consente all'istanza di gestione API di esporre in modo sicuro gli URL tramite SSL).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Aggiungere un certificato di dominio personalizzato al servizio gestione API

1. Selezionare **certificati** in **sicurezza**.
2. Selezionare **+ Aggiungi**.
3. Immettere un nome di risorsa per il certificato nel campo **ID** .
4. Selezionare il file contenente il certificato (. PFX) selezionando il campo **certificato** o l'icona della cartella adiacente.
5. Immettere la password per il certificato nel campo **password** .
6. Selezionare **Crea** per aggiungere il certificato al servizio gestione API.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Usare il portale di Azure per impostare un nome di dominio personalizzato per il gateway self-hosted

1. Selezionare i **gateway** in **Impostazioni**.
2. Selezionare il gateway self-hosted per cui si vuole configurare il nome di dominio.
3. Selezionare **nomi host** in **Impostazioni**.
4. Selezionare **+ Aggiungi**
5. Immettere il nome della risorsa per il nome host nel campo **nome** .
6. Immettere il nome di dominio nel campo nome **host** .
7. Selezionare un certificato dall'elenco a discesa **certificato** .
8. Selezionare la casella di controllo **Negotiate client certificate** se una delle API esposte tramite questo gateway usa l'autenticazione del certificato client.
    > [!WARNING]
    > Questa impostazione è condivisa da tutti i nomi di dominio configurati per il gateway.
9. Selezionare **Aggiungi** per assegnare il nome di dominio personalizzato al gateway self-hosted selezionato.

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare il servizio](upgrade-and-scale.md)
