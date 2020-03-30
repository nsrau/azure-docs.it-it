---
title: Come ottenere supporto per Avere vFXT per Azure
description: Spiegazione della procedura per l'apertura di ticket di supporto relativi ad Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252558"
---
# <a name="get-help-with-your-system"></a>Ottenere assistenza per il sistema

Per assistenza con il sistema Avere vFXT for Azure, ecco i modi per ottenere supporto:For help with your Avere vFXT for Azure system, here are the ways to get support:

* **Problemi con Avere vFXT**: usare il portale di Azure per aprire un ticket di supporto per Avere vFXT come descritto [di seguito](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota**: in caso di problemi relativi alle quota, [richiedere un aumento della quota](#request-a-quota-increase).
* **Documentazione ed esempi**: in caso di problemi inerenti a questa documentazione o agli esempi, scorrere fino alla fine della pagina contenente il problema e usare la sezione **Commenti** per eseguire la ricerca tra i problemi esistenti e registrarne uno nuovo se necessario.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Aprire un ticket di supporto per Avere vFXT

Se si verificano problemi durante la distribuzione o l'uso di Avere vFXT, richiedere assistenza tramite il portale di Azure.

Seguire questa procedura per assicurarsi che il ticket di supporto sia contrassegnato con una risorsa del cluster. L'assegnazione di un tag al ticket consente di inoltrarlo alla risorsa di supporto corretto.

1. Da [https://portal.azure.com](https://portal.azure.com), selezionare **Gruppi di risorse**. Passare al gruppo di risorse che contiene il cluster vFXT in cui si è verificato il problema e fare clic su una delle macchine virtuali del cluster Avere.

    ![Screenshot del pannello "Panoramica" del gruppo di risorse del portale di Azure con una macchina virtuale specifica evidenziata](media/avere-vfxt-ticket-vm.png)

1. Nella pagina della macchina virtuale scorrere fino alla fine del pannello a sinistra e fare clic su **Nuova richiesta di supporto**.

    ![Screenshot della pagina della macchina virtuale del portale di Azure per la macchina virtuale dello screenshot precedente. Il menu a sinistra viene fatto scorrere verso il basso e 'Nuova richiesta di supporto' è cerchiato.](media/avere-vfxt-ticket-request.png)

1. Nella prima pagina della richiesta di supporto scegliere il tipo di problema e assicurarsi che sia selezionata la sottoscrizione corretta.

   In **Servizio**fare clic su **Tutti i servizi** e cercare in **Archiviazione** per scegliere **Avere vFXT**.

   Aggiungere un breve riepilogo e selezionare il tipo di problema.

    ![schermata di una nuova schermata di richiesta di supporto nel portale di Azure.Screenshot of a new support request screen in the Azure portal. La scheda Nozioni di base è selezionata. Gli elementi della schermata includono Tipo di problema, Sottoscrizione, Servizio, Riepilogo e Tipo di problema.](media/ticket-basics.png)

   Scegliere **Avanti** per continuare.

1. La seconda pagina del modulo di supporto contiene suggerimenti per risolvere il problema in base alla descrizione di riepilogo. Fare clic sul pulsante **Avanti** nella parte inferiore se è ancora necessario creare un ticket di supporto.

   ![della nuova schermata di richiesta di supporto con la scheda Soluzioni selezionata. Un campo di testo al centro ha il titolo "Soluzione consigliata" e spiega possibili rimedi.](media/ticket-solutions.png)

1. Nella terza pagina, fornire dettagli: sono incluse informazioni sul cluster, l'ora in cui si è verificato il problema, la gravità e come contattare l'utente. Inserisci le informazioni e fai clic sul pulsante **Avanti** in basso.

   ![della nuova schermata di richiesta di supporto con la scheda Dettagli selezionata. I campi delle informazioni sono organizzati nelle categorie 'Dettagli problema', 'Metodo di supporto' e "Informazioni di contatto".](media/ticket-details.png)

1. Esaminare le informazioni nella pagina finale e fare clic su **Crea**. Una conferma e un numero di ticket verranno inviati all'indirizzo e-mail specificato e un membro del personale di supporto provvederà a contattare l'utente.

## <a name="request-a-quota-increase"></a>Richiedere un aumento della quota

Leggere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) per informazioni sui componenti necessari per la distribuzione di Avere vFXT per Azure. È possibile [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dal portale di Azure.
