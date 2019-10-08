---
title: Unità di test ospitata | Azure Marketplace
description: Come configurare e gestire un test drive ospitato nel Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 67d8421b2e545c951dcbc3280a306514e4b14897
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030062"
---
# <a name="hosted-test-drive"></a>Test drive ospitato

Un test drive ospitato permette di rimuovere la complessità di configurazione tramite Microsoft hosting e di gestire il servizio che esegue il provisioning e il deprovisioning dell'utente di test drive. Questo articolo è per gli editori che hanno l'offerta in AppSource o ne stanno compilando una nuova e desiderano offrire un test drive ospitato, che si connette con un'istanza di Dynamics 365 for Customer Engagement, Dynamics 365 per Finanza e operazioni o Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Come pubblicare un test drive

Passare a offerta esistente o crearne una nuova.

Selezionare l'opzione di test drive dal menu laterale.

Selezionare \'Sì\' per \'Abilitare un'opzione test drive\'.

Specificare i campi seguenti nella sezione \'Dettagli\'.

- **Descrizione**: Fornire una panoramica del test drive. Questo testo verrà mostrato all'utente durante il provisioning del test drive. Questo campo supporta l'HTML se si desidera fornire il contenuto formattato.
- **Manuale dell'utente**: Caricare un manuale utente dettagliato (file di tipo. pdf) che consente agli utenti di test drive di comprendere come usare l'app.
- **Video dimostrativo di test drive**: Facoltativamente, caricare un video che mostra l'app.

Concedere l'autorizzazione AppSource per effettuare il provisioning e il deprovisioning degli utenti di test drive nel tenant seguendo le istruzioni contenute [qui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

In questo passaggio, si generano i valori dell'\'Id dell'app Azure AD\' e della \'chiave dell'app Azure AD\' indicati di seguito.

Specificare i campi seguenti nella sezione \'Configurazione tecnica\':

- **Tipo di test drive**: Scegliere l'opzione \'Microsoft Hosted (ad esempio, Dynamics 365 per Customer Engagement). Ciò indica che Microsoft ospiterà e gestirà il servizio che esegue il provisioning e il deprovisioning dell'utente di test drive.
- **Numero massimo di unità di test simultanee**: Impostare questo campo sul numero di utenti simultanei che possono avere un test drive attivo in un determinato momento. Ogni utente userà una licenza Dynamics mentre il test drive è attivo, pertanto sarà necessario avere a disposizione almeno questo numero di licenze Dynamics disponibili per gli utenti del test drive. Valore consigliato da 3 a 5.
- **Durata test drive (ore)** : Impostare questo campo sul numero di ore per cui l'utente test drive sarà attivo. Trascorse le ore indicate, il tenant effettuerà il deprovisioning dell'utente. Valore consigliato da 2 a 24 ore, a seconda della complessità dell'app. Gli utenti possono sempre richiedere un altro test drive se non hanno più tempo a disposizione e desiderano accedere di nuovo al test drive.
- **URL istanza**: Consente di specificare un URL a cui l'utente test drive verrà inizialmente spostato all'avvio del test drive. In genere è l'URL dell'istanza di Dynamics 365 su cui sono installati i dati dell'app e dell'esempio. Valore di esempio: https: \//testdrive. CRM. Dynamics. com
- **ID Tenant Azure ad**: Fornire l'ID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere al portale di Azure e passare a \'Azure Active Directory\' -\> selezionare le proprietà dal pannello del menu -\> Copiare l'ID directory. Valore di esempio: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID app Azure ad**: ID del App Azure AD creato nel passaggio 7. \ esempio di valore: 53852862-a2ae-4e43-9461-faa49650a096
- **Chiave di app Azure ad**: Segreto per il App Azure AD creato nel passaggio 7. \ esempio di valore: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nome del Tenant Azure ad**: Specificare il nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato del \<tenantname.\> onmicrosoft.com. Valore di esempio: testdrive.onmicrosoft.com
- **URL dell'API Web dell'istanza**: Fornire l'URL dell'API Web per l'istanza di Dynamics 365. È possibile recuperare questo valore registrandosi nell'istanza di Microsoft Dynamics 365 e passando a Impostazioni -\> Personalizzazione -\> Risorse per gli sviluppatori -\> Istanza Web API (Copiare l'URL). Valore di esempio: https: \//testdrive. CRM. Dynamics. com/API/Data/v 9.0 
- **Nome ruolo**: Specificare il nome del ruolo di sicurezza Dynamics 365 personalizzato creato per test drive. Si tratta del ruolo che verrà assegnato agli utenti durante il test drive. Valore di esempio: testdriverole

## <a name="next-steps"></a>Passaggi successivi

Al termine **pubblicare** l'offerta e, una volta che l'app supera la certificazione, si otterrà un'**anteprima** dell'offerta. Avviare un test drive nell'interfaccia utente e verificare che i test drive siano eseguiti correttamente. Una volta acquisita familiarità con l'offerta di anteprima, è il momento di **passare alla fase operativa**
