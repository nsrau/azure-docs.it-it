---
title: Linee guida per le sottoscrizioni e gli account | Microsoft Docs
description: Informazioni sulle principali linee guida di progettazione e implementazione per le sottoscrizioni e gli account in Azure.
documentationcenter: ''
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou

---
# Linee guida per le sottoscrizioni e gli account
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione delle modalità di gestione delle sottoscrizioni e degli account mano a mano che si amplia l'ambiente e la base utenti.

## Linee guida per l'implementazione di sottoscrizioni e account
Decisioni:

* Quali sono i set di sottoscrizioni e account necessari per l'hosting dell'infrastruttura o del carico di lavoro IT?
* Come deve essere suddivisa la gerarchia in modo da adattarsi all'organizzazione?

Attività:

* Definire la gerarchia logica dell'organizzazione in base a come si intende gestirla a livello di sottoscrizioni.
* Per soddisfare questa gerarchia logica, definire gli account necessari e le sottoscrizioni per ogni account.
* Creare il set di sottoscrizioni e account usando la convenzione di denominazione scelta.

## Sottoscrizioni e account
Per usare Azure sono necessarie una o più sottoscrizioni ad Azure. Nell'ambito di tali sottoscrizioni sono presenti risorse come le macchine virtuali o le reti virtuali.

* I clienti aziendali dispongono in genere di una registrazione Enterprise, che è la risorsa più in alto nella gerarchia ed è associata a uno o più account.
* Per utenti e clienti senza una registrazione Enterprise, la risorsa di livello superiore è l'Account.
* Le sottoscrizioni sono associate agli account ed è possibile associare una o più sottoscrizioni a ogni account. In Azure le informazioni relative alla fatturazione vengono registrate a livello di sottoscrizione.

A causa del limite di due livelli gerarchici nella relazione di account/sottoscrizione è importante allineare la convenzione di denominazione degli account e delle sottoscrizioni alle esigenze di fatturazione. Se ad esempio un'azienda globale usa Azure, può scegliere di disporre di un account per ogni area e fare in modo che le sottoscrizioni vengano gestite a livello di area:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

È ad esempio possibile usare la struttura seguente:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Se in un'area si decide di associare più sottoscrizioni a un determinato gruppo, la convenzione di denominazione deve incorporare un modo per codificare i dati aggiuntivi sia nel nome account che nel nome della sottoscrizione. Questa organizzazione consente di elaborare i dati di fatturazione per generare nuovi livelli di gerarchia durante la fatturazione dei report:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

L'organizzazione potrebbe avere un aspetto simile al seguente:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Viene fornita la fatturazione dettagliata tramite un file scaricabile, per un singolo account o per tutti gli account di un contratto Enterprise.

## Passaggi successivi
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->