---
title: Come ottenere supporto per Avere vFXT per Azure
description: Spiegazione della procedura per l'apertura di ticket di supporto relativi ad Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 068747fd51779bd8a20636355a9ce2b032f6151d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255366"
---
# <a name="get-help-with-your-system"></a>Ottenere assistenza per il sistema

Se occorre assistenza con Avere vFXT per Azure, sono disponibili diversi modi per ottenere supporto:

* **Problemi con Avere vFXT**: usare il portale di Azure per aprire un ticket di supporto per Avere vFXT come descritto [di seguito](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota**: in caso di problemi relativi alle quota, [richiedere un aumento della quota](#request-a-quota-increase).
* **Documentazione ed esempi**: in caso di problemi inerenti a questa documentazione o agli esempi, scorrere fino alla fine della pagina contenente il problema e usare la sezione **Commenti** per eseguire la ricerca tra i problemi esistenti e registrarne uno nuovo se necessario.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Aprire un ticket di supporto per Avere vFXT

Se si verificano problemi durante la distribuzione o l'uso di Avere vFXT, richiedere assistenza tramite il portale di Azure.  

Seguire questa procedura per assicurarsi che il ticket di supporto sia contrassegnato con una risorsa del cluster. L'assegnazione di un tag al ticket consente di inoltrarlo alla risorsa di supporto corretto. 

1. Da [https://portal.azure.com](https://portal.azure.com) selezionare **Gruppi di risorse**.

   ![Screenshot del menu a sinistra del portale di Azure con "Gruppi di risorse" evidenziato](media/avere-vfxt-ticket-rg.png)

1. Passare al gruppo di risorse contenente il cluster vFXT in cui si è verificato il problema e fare clic su una delle macchine virtuali di Avere.

    ![Screenshot del pannello "Panoramica" del gruppo di risorse del portale di Azure con una macchina virtuale specifica evidenziata](media/avere-vfxt-ticket-vm.png)

1. Nella pagina della macchina virtuale scorrere fino alla fine del pannello a sinistra e fare clic su **Nuova richiesta di supporto**.

    ![Screenshot della pagina della macchina virtuale del portale di Azure per la macchina virtuale dello screenshot precedente. Si scorre fino alla fine del menu a sinistra e viene evidenziato "Nuova richiesta di supporto".](media/avere-vfxt-ticket-request.png)

1. Nella prima pagina della richiesta di supporto fare clic su **Tutti i servizi** e cercare in **Archiviazione** **Avere vFXT**.

    ![Screenshot della schermata Nuova richiesta di supporto nel portale di Azure con l'intestazione "Nozioni di base" e l'elemento Servizio evidenziato. Si seleziona il pulsante "Tutti i servizi" e il campo del menu a discesa contiene il valore "Avere vFXT".](media/avere-vfxt-ticket-service.png)

1. Nella seconda pagina scegliere il tipo di problema e la categoria corrispondenti al problema. Aggiungere un breve titolo e una descrizione che includa l'ora in cui si è verificato il problema. 

   ![Screenshot della schermata Nuova richiesta di supporto con l'intestazione "Problema", contenente molti campi da completare](media/avere-vfxt-ticket-problem.png)

1. Nella terza pagina immettere le informazioni di contatto e fare clic su **Crea**. Una conferma e un numero di ticket verranno inviati all'indirizzo e-mail specificato e un membro del personale di supporto provvederà a contattare l'utente.

## <a name="request-a-quota-increase"></a>Richiedere un aumento della quota

Leggere [Quota per il cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) per informazioni sui componenti necessari per la distribuzione di Avere vFXT per Azure. È possibile [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) dal portale di Azure.