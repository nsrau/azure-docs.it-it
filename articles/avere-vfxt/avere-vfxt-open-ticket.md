---
title: Come ottenere supporto per Avere vFXT per Azure
description: Spiegazione della procedura per l'apertura di ticket di supporto relativi ad Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372116"
---
# <a name="get-help-with-your-system"></a>Ottenere assistenza per il sistema

Per assistenza sul sistema vFXT per Azure, di seguito sono riportati i modi per ottenere supporto:

* **Problemi con Avere vFXT**: usare il portale di Azure per aprire un ticket di supporto per Avere vFXT come descritto [di seguito](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota**: in caso di problemi relativi alle quota, [richiedere un aumento della quota](#request-a-quota-increase).
* **Documentazione ed esempi**: in caso di problemi inerenti a questa documentazione o agli esempi, scorrere fino alla fine della pagina contenente il problema e usare la sezione **Commenti** per eseguire la ricerca tra i problemi esistenti e registrarne uno nuovo se necessario.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Aprire un ticket di supporto per Avere vFXT

Se si verificano problemi durante la distribuzione o l'uso di Avere vFXT, richiedere assistenza tramite il portale di Azure.

Seguire questa procedura per assicurarsi che il ticket di supporto sia contrassegnato con una risorsa del cluster. L'assegnazione di un tag al ticket consente di inoltrarlo alla risorsa di supporto corretto.

1. Da [https://portal.azure.com](https://portal.azure.com) selezionare **Gruppi di risorse**. Passare al gruppo di risorse che contiene il cluster vFXT in cui si è verificato il problema e fare clic su una delle macchine virtuali del cluster.

    ![Screenshot del pannello "Panoramica" del gruppo di risorse del portale di Azure con una macchina virtuale specifica evidenziata](media/avere-vfxt-ticket-vm.png)

1. Nella pagina della macchina virtuale scorrere fino alla fine del pannello a sinistra e fare clic su **Nuova richiesta di supporto**.

    ![Screenshot della pagina della macchina virtuale del portale di Azure per la macchina virtuale dello screenshot precedente. Il menu a sinistra viene spostato verso il basso e la nuova richiesta di supporto è racchiusa tra parentesi.](media/avere-vfxt-ticket-request.png)

1. Nella prima pagina della richiesta di supporto scegliere il tipo di problema e verificare che sia selezionata la sottoscrizione corretta.

   In **servizio**fare clic su **tutti i servizi** e cercare in **archiviazione** per scegliere **vFXT**.

   Aggiungere un breve riepilogo e selezionare il tipo di problema.

    ![Screenshot di una nuova schermata di richiesta di supporto nella portale di Azure. È selezionata la scheda nozioni di base. Gli elementi della schermata includono tipo di problema, sottoscrizione, servizio, riepilogo e tipo di problema.](media/ticket-basics.png)

   Fare clic su **Avanti** per continuare.

1. La seconda pagina del modulo di supporto contiene suggerimenti per la risoluzione del problema in base alla descrizione di riepilogo. Se è ancora necessario creare un ticket di supporto, fare clic sul pulsante **Avanti** in basso.

   ![screenshot della schermata nuova richiesta di supporto con la scheda soluzioni selezionata. Un campo di testo al centro ha il titolo "soluzione consigliata" e spiega le possibili soluzioni.](media/ticket-solutions.png)

1. Nella terza pagina specificare i dettagli: sono incluse informazioni sul cluster, l'ora in cui si è verificato il problema, la gravità e la modalità di contatto. Immettere le informazioni e fare clic sul pulsante **Avanti** nella parte inferiore.

   ![screenshot della schermata nuova richiesta di supporto con la scheda Dettagli selezionata. I campi informativi sono organizzati nelle categorie "Dettagli problema", "metodo di supporto" e "informazioni di contatto".](media/ticket-details.png)

1. Esaminare le informazioni nella pagina finale e fare clic su **Crea**. Una conferma e un numero di ticket verranno inviati all'indirizzo e-mail specificato e un membro del personale di supporto provvederà a contattare l'utente.

## <a name="request-a-quota-increase"></a>Richiedere un aumento della quota

Leggere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) per informazioni sui componenti necessari per la distribuzione di Avere vFXT per Azure. È possibile [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dal portale di Azure.
