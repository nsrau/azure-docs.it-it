---
title: Test drive ospitato | Microsoft Docs
description: Come configurare e gestire un test drive ospitato nel Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 1ab9fcd50ad7081f8047d62e545287fa75db75e4
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809006"
---
# <a name="hosted-test-drive"></a>Test drive ospitato

Un test drive ospitato permette di rimuovere la complessità di configurazione tramite Microsoft hosting e di gestire il servizio che esegue il provisioning e il deprovisioning dell'utente di test drive. Questo articolo è per gli editori che hanno l'offerta in AppSource o ne stanno compilando una nuova e desiderano offrire un test drive ospitato, che si connette con un'istanza di Dynamics 365 for Customer Engagement, Dynamics 365 per Finanza e operazioni o Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Come pubblicare un test drive

Passare a offerta esistente o crearne una nuova.

Selezionare l'opzione di test drive dal menu laterale.

Selezionare \'Sì\' per \'Abilitare un'opzione test drive\'.

Specificare i campi seguenti nella sezione \'Dettagli\'.

- **Descrizione**: fornire una panoramica del test drive. Questo testo verrà mostrato all'utente durante il provisioning del test drive. Questo campo supporta l'HTML se si desidera fornire il contenuto formattato.
- **Manuale dell'utente**: caricare un manuale dell'utente dettagliato (file di tipo PDF) che consente agli utenti di test drive di comprendere come usare l'app.
- **Test Drive Demo Video** (Video demo del test drive): se lo si desidera, caricare un video di presentazione dell'app.

Concedere l'autorizzazione AppSource per effettuare il provisioning e il deprovisioning degli utenti di test drive nel tenant seguendo le istruzioni contenute [qui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

In questo passaggio, si generano i valori dell'\'Id dell'app Azure AD\' e della \'chiave dell'app Azure AD\' indicati di seguito.

Specificare i campi seguenti nella sezione \'Configurazione tecnica\':

- **Tipo di test drive**: scegliere l'opzione \'Hosted Microsoft (ad esempio Dynamics 365 for Customer Engagement)". Ciò indica che Microsoft ospiterà e gestirà il servizio che esegue il provisioning e il deprovisioning dell'utente di test drive.
- **Numero massimo di test drive simultanei**: impostare questo campo per indicare il numero massimo di utenti simultanei che possono disporre di un test drive attivo in qualsiasi momento. Ogni utente userà una licenza Dynamics mentre il test drive è attivo, pertanto sarà necessario avere a disposizione almeno questo numero di licenze Dynamics disponibili per gli utenti del test drive. Valore consigliato da 3 a 5.
- **Durata del test drive (ore)**: impostare questo campo per indicare per quante ore il test drive sarà attivo per gli utenti. Trascorse le ore indicate, il tenant effettuerà il deprovisioning dell'utente. Valore consigliato da 2 a 24 ore, a seconda della complessità dell'app. Gli utenti possono sempre richiedere un altro test drive se non hanno più tempo a disposizione e desiderano accedere di nuovo al test drive.
- **URL dell'istanza**: fornire un URL a cui l'utente di test drive verrà inizialmente indirizzato all'avvio del test drive. In genere è l'URL dell'istanza di Dynamics 365 su cui sono installati i dati dell'app e dell'esempio. Valore di esempio:https://testdrive.crm.dynamics.com
- **ID del tenant di Azure AD**: fornire l'ID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere al portale di Azure e passare a \'Azure Active Directory\' -\> selezionare le proprietà dal pannello del menu -\> Copiare l'ID directory. Valore di esempio: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID app Azure AD**: ID dell'app Azure AD creato nel passaggio 7.\ Valore di esempio: 53852862-a2ae-4e43-9461-faa49650a096
- **Chiave di app Azure AD**: segreta per l'app Azure AD creata nel passaggio 7. \ Valore di esempio: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Nome del Tenant di Azure AD**: specificare il nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato del \<tenantname.\> onmicrosoft.com. Valore di esempio: testdrive.onmicrosoft.com
- **URL dell'API Web dell'istanza**: specificare l'URL dell'API Web per l'istanza di Dynamics 365. È possibile recuperare questo valore registrandosi nell'istanza di Microsoft Dynamics 365 e passando a Impostazioni -\> Personalizzazione -\> Risorse per gli sviluppatori -\> Istanza Web API (Copiare l'URL). Valore di esempio: https://testdrive.crm.dynamics.com/api/data/v9.0 
- **Nome del ruolo**: specificare il nome del ruolo di sicurezza personalizzato di Dynamics 365 creato per il test drive. Si tratta del ruolo che verrà assegnato agli utenti durante il test drive. Valore di esempio: testdriverole

## <a name="next-steps"></a>Passaggi successivi

Al termine **pubblicare** l'offerta e, una volta che l'app supera la certificazione, si otterrà un'**anteprima** dell'offerta. Avviare un test drive nell'interfaccia utente e verificare che i test drive siano eseguiti correttamente. Una volta acquisita familiarità con l'offerta di anteprima, è il momento di **passare alla fase operativa**
