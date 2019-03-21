---
title: Scheda Test Drive di Dynamics 365 for Customer Engagement - Azure Marketplace | Microsoft Docs
description: Come configurare il test drive per un'offerta di applicazione Dynamics 365 for Customer Engagement nel marketplace AppSource.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 373312b4c7f05fe41c9ca8165b8ff6f1b0e56f1b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454970"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Scheda Test Drive dell'applicazione Dynamics 365 for Customer Engagement

Usare la scheda **Test Drive** per offrire una versione di valutazione per i clienti.  Offre ai clienti una versione di valutazione pratica e autoguidata delle funzionalità e dei vantaggi principali dell'offerta, provati in uno scenario di implementazione reale.  Tra le opzioni di valutazione disponibili, Test drive è la più efficace per la generazione di lead di qualità elevata e una maggior conversione dei lead.  Per altre informazioni, vedere [Informazioni sul test drive](../test-drive/what-is-test-drive.md).

L'esperienza di Test Drive per le applicazioni Dynamics 365 viene eseguita automaticamente come soluzione ospitata da Microsoft.  Per altre informazioni, vedere [Test drive ospitato](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

La scheda Test Drive ha tre sezioni potenziali: **Test Drive**, **Details** (Dettagli) e **Technical Configuration** (Configurazione tecnica).  Le ultime due sezioni vengono visualizzate solo dopo aver abilitato la funzionalità Test Drive.  Un asterisco (*) alla fine del nome del campo indica che si tratta di un campo obbligatorio. 


## <a name="test-drive-section"></a>Sezione Test Drive

Per abilitare questa funzionalità, selezionare **Yes** (Sì) in **Enable a Test Drive** (Abilita un test drive).


## <a name="details-section"></a>Sezione Details (Dettagli)

Nella sezione **Details** (Dettagli) si forniscono le informazioni di base sul Test Drive.   

![Sezione dei dettagli Test Drive](./media/test-drive-tab-details.png)

Nella tabella seguente vengono descritti i campi necessari per configurare il test drive per l'applicazione Dynamics 365.

|      Campo                    |    DESCRIZIONE                  |
|    ---------                  |  ---------------                |
|      DESCRIZIONE              |   Descrivere le operazioni che possono essere eseguite durante il test drive. È possibile usare tag HTML di base per formattare questa descrizione. Ad esempio, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e le intestazioni.  |
|  Manuale dell'utente                  |   Caricare un manuale dell'utente che i clienti possono usare per eseguire il test drive. Questo documento deve essere un file con estensione pdf.              |
|  Video demo del test drive (facoltativo) |  È possibile fornire una procedura dettagliata video del test drive che un cliente può guardare prima di eseguire un test drive. Specificare un URL per il video su YouTube o Vimeo. Se si seleziona **+ Aggiungi video**, verrà chiesto di fornire le informazioni seguenti:<ul><li>NOME</li><li>URL</li><li>Anteprima (in formato PNG, 533 x 324 pixel)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Sezione Technical Configuration (Configurazione tecnica)

In questa sezione si specificano i dettagli tecnici sul test drive.

![Sezione dei dettagli Test Drive](./media/test-drive-tab-tech-config.png)

I campi hanno gli scopi seguenti:

|      Campo                    |    DESCRIZIONE                  |
|    ---------                  |  ---------------                |
| Type of Test Drive (Tipo di test drive)            | Scegliere **Microsoft Hosted (Ospitato da Microsoft) (Dynamics 365 for Customer Engagement)**.  |
| Max Concurrent Test Drives (Numero massimo di test drive simultanei)    | Numero massimo di istanze simultanee di un test drive attivo in qualsiasi momento. Ogni utente userà una licenza Dynamics mentre il test drive è attivo, pertanto sarà necessario avere a disposizione almeno questo numero di licenze Dynamics disponibili per gli utenti del test drive. Valore consigliato da 3 a 5.  |
| Durata del test drive (ore)   | Numero massimo di ore per cui sarà attiva l'istanza di test drive dell'utente. Trascorso questo periodo, verrà effettuato il deprovisioning dell'istanza dal tenant. Il valore consigliato è di 2-24 ore, a seconda della complessità dell'app. Gli utenti possono sempre richiedere un altro test drive se non hanno più tempo a disposizione e vogliono ripetere la valutazione.  |
| Instance URL (URL istanza)                  | URL di navigazione iniziale del test drive. In genere è l'URL dell'istanza di Dynamics 365 su cui sono installati i dati dell'app e dell'esempio.  |
| Azure AD Tenant ID (ID del tenant di Azure AD)            | GUID del tenant di Azure per l'istanza di Dynamics 365. Per recuperare questo valore, accedere al portale di Azure e passare a **Azure Active Directory** > **Selezionare Proprietà** > **Copiare l'ID directory**.  |
| Azure AD App ID (ID App Azure AD)               | GUID dell'applicazione Azure AD  |
| Chiave App Azure AD              | Segreto dell'applicazione Azure AD, ad esempio: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Azure AD Tenant Name (Nome del tenant di Azure AD)          | Nome del tenant di Azure per l'istanza di Dynamics 365. Usare il formato <nometenant.>onmicrosoft.com, ad esempio: `testdrive.onmicrosoft.com`.  |
| Instance Web API URL (URL API Web dell'istanza)          | URL dell'API Web per l'istanza di Dynamics 365. È possibile recuperare questo valore accedendo all'istanza di Microsoft Dynamics 365 e passando a **Impostazioni** > **Personalizzazione** > **Risorse per sviluppatori** > **API Web istanza (copiare quest'URL)**. Valore di esempio: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Nome del ruolo                     | Nome del ruolo di sicurezza personalizzato di Dynamics 365 creato per il test drive e che verrà assegnato agli utenti all'esecuzione, ad esempio `testdriveuser`. |
|  |  |

Dopo aver fornito tutte le informazioni necessarie, selezionare **Save** (Salva).


## <a name="next-steps"></a>Passaggi successivi

Nel passaggio successivo si forniranno le informazioni di vendita e marketing nella [scheda Storefront Details (Dettagli vetrina)](./cpp-storefront-details-tab.md).

