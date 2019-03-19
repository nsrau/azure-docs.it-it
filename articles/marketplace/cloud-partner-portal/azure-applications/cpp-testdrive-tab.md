---
title: Test drive per un'offerta di applicazione Azure | Microsoft Docs
description: Come configurare il test drive per un'offerta di applicazione Azure in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: d51c7330acd37d8a861c136ea85a2ff456eb264e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446487"
---
# <a name="azure-applications-test-drive-tab"></a>Scheda Test drive per le applicazioni Azure

Usare la scheda Test drive per offrire una versione di valutazione per i clienti.

## <a name="test-drive-benefits"></a>Vantaggi del test drive

La creazione di una versione di valutazione per i clienti è il modo migliore per garantire loro la validità dell'eventuale acquisto. Tra le opzioni di valutazione disponibili, Test drive è la più efficace per la generazione di lead di qualità elevata e una maggior conversione dei lead.

Offre ai clienti una versione di valutazione pratica e autoguidata delle funzionalità e dei vantaggi principali del prodotto, provati in uno scenario di implementazione reale.

## <a name="how-a-test-drive-works"></a>Funzionamento di un test drive

Un cliente potenziale ricerca e individua l'applicazione in Marketplace. Il cliente effettua l'accesso e accetta le condizioni per l'utilizzo. Il cliente riceve quindi un ambiente preconfigurato per provare il prodotto per un determinato numero di ore, mentre si riceve un lead altamente qualificato di cui eseguire il follow-up. Per altre informazioni, vedere [Informazioni sul test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

## <a name="setting-up-a-test-drive"></a>Configurazione di un test drive

Usare la procedura seguente per abilitare e configurare un test drive.

### <a name="to-enable-a-test-drive"></a>Per abilitare un test drive:

1. In **Nuova offerta** selezionare la scheda **Test drive**.
2. In **Test drive** selezionare **Sì** per **Enable a Test Drive** (Abilita un test drive).

   ![Abilitare un test drive](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Per configurare un test drive:

Dopo aver abilitato un test drive, sarà necessario compilare i moduli seguenti per configurare il test drive:
  
 - Dettagli
 - Configurazione tecnica
 - Dettagli di sottoscrizione della distribuzione del test drive

La schermata successiva mostra tutti i moduli di Test drive. Un asterisco (*) alla fine del nome del campo indica che si tratta di un campo obbligatorio. 

![Configurare un test drive](./media/managed-app-configure-testdrive.png)

Nella tabella seguente vengono descritti i campi necessari per configurare il test drive per l'applicazione gestita.

|    **Campo**       |  **Descrizione**  |
|  ---------------   |  ---------------  |
|      DESCRIZIONE              |   Descrivere le operazioni che possono essere eseguite durante il test drive. È possibile usare tag HTML di base per formattare questa descrizione. Ad esempio, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e le intestazioni.                |
|    Manuale dell'utente                |     Caricare un manuale dell'utente che i clienti possono usare per eseguire il test drive. Questo documento deve essere un file con estensione pdf.              |
|         Video demo del test drive (facoltativo)           |       È possibile fornire una procedura dettagliata video del test drive che un cliente può guardare prima di eseguire un test drive. Specificare un URL per il video su YouTube o Vimeo. Se si seleziona **+ Aggiungi video**, verrà chiesto di fornire le informazioni seguenti:<ul><li>NOME</li><li>URL</li><li>Anteprima (in formato PNG, 533 x 324 pixel)</li></ul>            |
|       Istanze             |        Configurare il numero di istanze desiderate, in quali aree e la velocità con cui i clienti possono ottenere il test drive. Per altre informazioni, vedere [Come pubblicare un test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
|       Durata del test drive (ore)             |       Immettere un numero intero per il numero di ore. L'intervallo consentito va da 1 a 999.            |
|        Modello ARM del test drive            |        Caricare un file compresso (con estensione zip) contenente i modelli di Azure Resource Manager per l'app. Per altre informazioni, vedere [Test drive di Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).            |
|        Informazioni di accesso            |         Specificare le informazioni di accesso dopo che un cliente ha ottenuto il test drive. Ad esempio, un URL per accedere al test drive e le informazioni di accesso. . È possibile usare tag HTML di base per formattare questa descrizione. Ad esempio, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e le intestazioni.          |
|       ID sottoscrizione di Azure             |       Si concede l'accesso ai servizi di Azure e al portale di Azure. Nella sottoscrizione si tiene traccia dell'utilizzo delle risorse e vengono fatturati i servizi. Se non si dispone ancora di una sottoscrizione separata di Azure solo per i test drive, creare una sottoscrizione.             |
|          ID del tenant di Azure AD          |        Specificare un tenant esistente in Azure Active Directory o creare un tenant per questo test drive.           |
|         ID App Azure AD           |       Creare e registrare una nuova applicazione. Microsoft usa questa applicazione per eseguire operazioni nell'istanza di test drive.            |
|          Chiave App Azure AD          |         Creare una chiave di autenticazione per l'app e incollarla in questo campo.          |

Dopo avere specificato tutte le informazioni richieste, selezionare **Salva** per completare la configurazione del test drive.

## <a name="next-steps"></a>Passaggi successivi

[Scheda Marketplace](./cpp-marketplace-tab.md)