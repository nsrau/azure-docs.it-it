---
title: Configurare un nome di dominio personalizzato per l'istanza di Gestione API di Azure | Microsoft Docs
description: Questo argomento descrive come configurare un nome di dominio personalizzato per l'istanza di Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: 0f1c9e8e8298647721f16962d3f6756c74f8c8ef
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="configure-a-custom-domain-name"></a>Configurare un nome di dominio personalizzato 

Quando si crea un'istanza di Gestione API (APIM), Azure la assegna a un sottodominio di azure-api.net (ad esempio `apim-service-name.azure-api.net`). È tuttavia possibile esporre gli endpoint dell'APIM usando il proprio nome di dominio, ad esempio **contoso.com**. Questa esercitazione illustra come eseguire il mapping di un nome DNS personalizzato esistente agli endpoint esposti da un'istanza di Gestione API di Azure.

## <a name="prerequisites"></a>prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

+ Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
+ Un nome di dominio personalizzato di proprietà dell'utente. Il nome di dominio personalizzato che si vuole usare deve essere acquistato separatamente e ospitato in un server DNS. Questo argomento fornisce istruzioni su come ospitare un nome di dominio personalizzato.
+ È necessario avere un certificato valido con una chiave pubblica e privata (.PFX). Il soggetto o il nome alternativo del soggetto (SAN) deve corrispondere al nome di dominio (questo consente ad APIM di esporre in modo sicuro gli URL tramite SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usare il portale di Azure per impostare un nome di dominio personalizzato

1. Nel [portale di Azure](https://portal.azure.com/) passare all'istanza di Gestione API.
2. Selezionare **Domini e SSL personalizzati**.
    
    Ci sono molti endpoint a cui è possibile assegnare un nome di dominio personalizzato. Attualmente sono disponibili gli endpoint seguenti: 
    + **Proxy** (il valore predefinito è: `<apim-service-name>.azure-api.net`), 
    + **Portale** (il valore predefinito è: `<apim-service-name>.portal.azure-api.net`),     
    + **Gestione** (il valore predefinito è: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (il valore predefinito è: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > È possibile aggiornare tutti gli endpoint o alcuni di essi. In genere, i clienti aggiornano **Proxy** (questo URL viene usato per chiamare l'API esposta tramite Gestione API) e **Portale** (l'URL del portale per sviluppatori). Gli endpoint **Gestione** e **SCM** vengono usati internamente dai clienti APIM e pertanto è meno frequente che sia assegnato a essi un nome di dominio personalizzato.
3. Selezionare l'endpoint che si vuole aggiornare. 
4. Nella finestra di destra fare clic su **Personalizzato**.

    + In **Nome di dominio personalizzato** specificare il nome che si vuole usare. Ad esempio, `api.contoso.com`. <br/>Sono supportati anche i nomi di dominio con caratteri jolly (ad esempio *.dominio.com).
    + In **Certificato** specificare un file .PFX valido che si vuole caricare. 
    + Se il certificato ha una password, immetterla nel campo **Password**.
1. Fare clic su Applica.

    >[!NOTE]
    >Il processo di assegnazione del certificato potrebbe richiedere circa 15 minuti o più, a seconda delle dimensioni della distribuzione. Developer SKU ha tempi di inattività, mentre SKU Basic e superiore non ne ha.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare e ridimensionare il servizio](upgrade-and-scale.md)