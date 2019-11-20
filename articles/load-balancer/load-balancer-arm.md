---
title: Supporto Azure Resource Manager per Load Balancer
description: In questo articolo, usare Azure PowerShell e modelli con Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: b22b89334fbb55e594ac2b27b486cf5d0bd26f03
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196113"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Supporto Azure Resource Manager con Azure Load Balancer



Azure Resource Manager è il framework di gestione preferito dei servizi in Azure. È ora possibile gestire Azure Load Balancer con API e strumenti basati su Azure Resource Manager.

## <a name="concepts"></a>Concetti

Con Resource Manager, Azure Load Balancer contiene le seguenti risorse figlio:

* Configurazione IP front-end: un servizio di bilanciamento del carico può includere uno o più indirizzi IP front-end, noti anche come IP virtuali (VIP). Questi indirizzi IP vengono usati come ingresso per il traffico.
* Pool di indirizzi back-end: questo pool è una raccolta di indirizzi IP associati alla scheda di interfaccia di rete (NIC) della macchina virtuale a cui viene distribuito il carico.
* Regole di bilanciamento del carico: una proprietà della regola esegue il mapping di una specifica combinazione di IP e porte front-end a un set di combinazioni di indirizzi IP e porte back-end. Un singolo servizio di bilanciamento del carico può avere più regole di bilanciamento del carico. Ogni regola è una combinazione di indirizzo IP e porta front-end e di IP e porta back-end associati alle macchine virtuali.
* Probe: le probe consentono di tenere traccia dell'integrità delle istanze della macchina virtuale. Se un probe di integrità non riesce, l'istanza della macchina virtuale viene esclusa automaticamente dalla rotazione.
* Regole NAT in ingresso: regole NAT che definiscono il traffico in ingresso che attraversa l'IP front-end e viene distribuito all'IP back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelli di Guida introduttiva

Gestione risorse di Azure consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.

I modelli possono includere definizioni per:
* **Macchine virtuali**
* **Reti virtuali**
* **Set di disponibilità**
* **Interfacce di rete (NIC)**
* **Account di archiviazione**
* **Servizi di bilanciamento del carico**
* **Gruppi di sicurezza di rete**
* **Indirizzi IP pubblici.** 

Con i modelli è possibile creare tutto il necessario per un'applicazione complessa. Il file modello può essere verificato nel sistema di gestione dei contenuti per il controllo della versione e la collaborazione.

[Altre informazioni sui modelli](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Altre informazioni sulle risorse di rete](../networking/networking-overview.md)

Per i modelli di avvio rapido con Azure Load Balancer, vedere il [repository GitHub](https://github.com/Azure/azure-quickstart-templates) che ospita un set di modelli generati dalla community.

Esempi di modelli:

* [2 macchine virtuali in un bilanciamento del carico e regole di bilanciamento del carico](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 macchine virtuali in una VNET con regole di bilanciamento del carico e Load Balancer interne](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 macchine virtuali in un servizio di bilanciamento del carico e configurare le regole NAT in LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurazione del bilanciamento del carico di Azure con PowerShell o l'interfaccia della riga di comando

Introduzione ai cmdlet di Azure Resource Manager, agli strumenti da riga di comando e alle API REST

* [cmdlet di rete di Azure](https://docs.microsoft.com/powershell/module/az.network#networking) possono essere usati per creare un bilanciamento del carico.
* [Come creare un servizio di bilanciamento del carico tramite Gestione risorse di Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md)
* [API REST di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Passaggi successivi

Iniziare [a creare un servizio di bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md) e configurare il tipo di [modalità di distribuzione](load-balancer-distribution-mode.md) per il comportamento specifico del traffico di rete.

Informazioni su come gestire [le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Queste impostazioni sono importanti quando è necessario che l'applicazione mantenga le connessioni attive per i server dietro un servizio di bilanciamento del carico.
