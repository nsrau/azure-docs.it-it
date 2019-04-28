---
title: Configurazione del test drive per un'offerta di applicazioni SaaS di Azure | Microsoft Docs
description: Configurare il test drive per un'offerta di applicazioni SaaS in Azure Marketplace.
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
ms.date: 04/24/2019
ms.author: pbutlerm
ms.openlocfilehash: b12ba53f847b46479b3100c088c29372b58c1b8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594216"
---
# <a name="saas-application-test-drive-tab"></a>Scheda Test drive per le applicazioni SaaS

Usare la scheda Test drive per offrire una versione di valutazione per i clienti.

## <a name="test-drive-benefits"></a>Vantaggi del test drive

La creazione di una versione di valutazione per i clienti è il modo migliore per garantire loro la validità dell'eventuale acquisto. Tra le opzioni di valutazione disponibili, Test drive è la più efficace per la generazione di lead di qualità elevata e una maggior conversione dei lead.

Il test drive offre ai clienti una versione di valutazione pratica e autoguidata delle funzionalità e dei vantaggi principali del prodotto, provati in uno scenario di implementazione reale.


## <a name="how-a-test-drive-works"></a>Funzionamento di un test drive

Un cliente potenziale ricerca e individua l'applicazione in Marketplace. Il cliente effettua l'accesso e accetta le condizioni per l'utilizzo. Il cliente riceve quindi un ambiente preconfigurato per provare il prodotto per un determinato numero di ore, mentre si riceve un lead altamente qualificato di cui eseguire il follow-up. Per altre informazioni, vedere [Informazioni sul test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).


## <a name="publishing-steps"></a>Procedura per la pubblicazione

I passaggi principali della pubblicazione per l'aggiunta di un test drive sono i seguenti:

1. Definire lo scenario del test drive
2. Creare e/o modificare il modello di Resource Manager
3. Eseguire i singoli passaggi per creare manualmente il test drive
4. Ripubblicare l'offerta


## <a name="setting-up-a-test-drive"></a>Configurazione di un test drive

Sono disponibili quattro tipi di test drive, ognuno basato sul tipo di prodotto, scenario e marketplace.

|  **Tipo**          |  **Descrizione**  |  **Istruzioni di configurazione**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Un test drive di Azure Resource Manager è un modello di distribuzione che contiene tutte le risorse di Azure che includono una soluzione compilata dall'editore. I prodotti che rientrano in questo tipo di test drive sono quelli che usano solo risorse di Azure.               |       [Test drive di Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Ospitato             |       Un test drive ospitato permette di rimuovere la complessità di configurazione tramite Microsoft hosting e di gestire il servizio che esegue il provisioning e il deprovisioning dell'utente di test drive.             |         [Test drive ospitato](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      App per la logica              |       Un test drive di app per la logica è un modello di distribuzione progettato per includere tutte le architetture di soluzioni complesse. Tutte le applicazioni di Dynamics e i prodotti personalizzati dovrebbero usare questo tipo di test drive.            |      [Test drive di app per la logica](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Un test drive di Power BI è costituito da un collegamento incorporato a un dashboard personalizzato. È consigliabile usare questo tipo di test drive per tutti i prodotti per cui si vuole offrire la valutazione di un oggetto visivo di Power BI interattivo. È sufficiente caricare l'URL di Power BI incorporato.          |        [Test drive di Power BI](#power-bi-test-drive)           |
|   |   |   |


### <a name="power-bi-test-drive"></a>Test drive di Power BI

Usare la procedura seguente per configurare un test drive.

1. In Nuova offerta selezionare **Test drive**.
2. In Test drive selezionare **Sì**.

   ![Abilitare il test drive](./media/saas-enable-test-drive.png)

   Quando si abilita un test drive, verranno visualizzati i moduli Dettagli e Configurazione tecnica, riportati nella schermata successiva.

   ![Modulo di configurazione del test drive](./media/saas-test-drive-yes.png)

3. In **Dettagli** specificare le informazioni per i campi seguenti:
  
   - Descrizione: descrivere il test drive e le operazioni che gli utenti possono eseguire. È possibile usare tag HTML di base per formattare questa descrizione.
   - Manuale dell'utente: caricare un documento del Manuale dell'utente utilizzabile dai clienti durante l'esecuzione del test drive. Il manuale deve essere un file con estensione pdf.
   - Video demo del test drive (facoltativo): è possibile fornire un video (YouTube o Vimeo) che i clienti possono guardare prima di eseguire il test drive. Specificare un URL per il video.

4. In **Configurazione tecnica** specificare le informazioni per i campi seguenti:

   - Tipo di test drive: selezionare **Power BI** nell'elenco a discesa.
   - Link to Power BI Dashboard (Collegamento al dashboard di Power BI): specificare un collegamento al dashboard.

5. Al termine della configurazione del test drive, selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

[Scheda Dettagli vetrina](./cpp-storefront-tab.md)
