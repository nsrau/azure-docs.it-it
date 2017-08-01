---
title: Migrazione della piattaforma del Centro sicurezza di Azure | Microsoft Docs
description: "Questo documento illustra alcune delle modifiche che sono state apportate alla modalità di raccolta dati nel Centro sicurezza di Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.contentlocale: it-it
ms.lasthandoff: 07/24/2017

---
# <a name="azure-security-center-platform-migration"></a>Migrazione della piattaforma del Centro sicurezza di Azure

A partire dall'inizio di giugno 2017, il Centro sicurezza di Azure ha apportato modifiche importanti alle modalità di raccolta e archiviazione dei dati di protezione.  Queste modifiche garantiscono nuove funzionalità come la possibilità di eseguire facilmente ricerche nei dati di protezione e assicurano un migliore allineamento con gli altri servizi di gestione e monitoraggio di Azure.

> [!NOTE]
> La migrazione della piattaforma non dovrebbe influire sulle risorse di produzione e non richiede alcun intervento da parte dell'utente.


## <a name="whats-happening-during-this-platform-migration"></a>Cosa succede durante la migrazione della piattaforma?

In precedenza, il Centro sicurezza usava l'agente di monitoraggio di Azure per raccogliere i dati di protezione dalle macchine virtuali, tra cui informazioni sulle configurazioni di sicurezza, usate per identificare le vulnerabilità, ed eventi di sicurezza, usati per rilevare le minacce. Questi dati venivano archiviati negli account di archiviazione in Azure.

Attualmente, il Centro sicurezza usa Microsoft Monitoring Agent, lo stesso agente usato da Operations Management Suite e dal servizio Log Analytics. I dati raccolti dall'agente vengono archiviati in un'*area di lavoro* di [Log Analytics](../log-analytics/log-analytics-manage-access.md) esistente associata alla sottoscrizione di Azure o in una o più aree di lavoro nuove, a seconda della georilevazione della macchina virtuale.

## <a name="agent"></a>Agente

Nel corso della transizione, Microsoft Monitoring Agent (per [Windows](../log-analytics/log-analytics-windows-agents.md) o [Linux](../log-analytics/log-analytics-linux-agents.md)) viene installato in tutte le VM di Azure da cui vengono attualmente raccolti i dati.  Se la macchina virtuale ha già installato Microsoft Monitoring Agent, il Centro sicurezza usa l'agente installato corrente.

Per un periodo di tempo (in genere alcuni giorni), entrambi gli agenti verranno eseguiti contemporaneamente per agevolare la transizione senza alcuna perdita di dati. In questo modo, Microsoft sarà in grado di garantire che la nuova pipeline di dati sia operativa prima di sospendere l'uso di quella corrente. Al termine della verifica, l'agente di monitoraggio di Azure verrà rimosso dalle macchine virtuali, senza che sia richiesto alcun intervento da parte dell'utente. Verrà inviato un messaggio e-mail quando tutti i clienti sono stati migrati.
 
Non è consigliabile disinstallare manualmente l'agente di monitoraggio di Azure durante la migrazione perché potrebbe comportare interruzioni nei dati di protezione. Contattare il [Servizio Supporto Tecnico Clienti Microsoft](https://support.microsoft.com/contactus/) per ulteriore assistenza. 

Microsoft Monitoring Agent per Windows richiede l'uso della porta TCP 443. Per altre informazioni, vedere [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](security-center-troubleshooting-guide.md).


> [!NOTE] 
> Poiché Microsoft Monitoring Agent può essere usato da altri servizi di gestione e monitoraggio di Azure, l'agente non sarà disinstallato automaticamente quando si disattiva la raccolta dati nel Centro sicurezza. Tuttavia, è possibile disinstallare manualmente l'agente se necessario.

## <a name="workspace"></a>Area di lavoro

Come descritto in precedenza, i dati raccolti da Microsoft Monitoring Agent (per conto del Centro sicurezza) vengono archiviati in una o più aree di lavoro di Log Analytics esistenti associate alla sottoscrizione di Azure o in una o più aree di lavoro nuove, a seconda della georilevazione della macchina virtuale.

Nel portale di Azure è possibile visualizzare un elenco delle aree di lavoro di Log Analytics, incluse quelle create dal Centro sicurezza. Per le nuove aree di lavoro verrà creato un gruppo di risorse correlato. Entrambi seguono questa convenzione di denominazione:

- Area di lavoro: *DefaultWorkspace-[subscription-ID]-[geo]*
- Gruppo di risorse: *DefaultResouceGroup-[geo]* 
 
Per le aree di lavoro create dal Centro sicurezza, i dati vengono conservati per 30 giorni. Per le aree di lavoro esistenti, il periodo di memorizzazione dipende dal piano tariffario dell'area di lavoro.

> [!NOTE]
> I dati raccolti in precedenza dal Centro sicurezza rimangono negli account di archiviazione. Al termine della migrazione, è possibile eliminare questi account di archiviazione.

### <a name="oms-security-solution"></a>Soluzione di sicurezza di OMS 

Per i clienti esistenti che non dispongono della soluzione di sicurezza di OMS, la soluzione viene installata automaticamente nell'area di lavoro, ma solo per le macchine virtuali di Azure. Non disinstallare questa soluzione, dal momento che non è disponibile alcuna correzione automatica se questa operazione viene eseguita dalla console di gestione di OMS.


## <a name="other-updates"></a>Altri aggiornamenti

In combinazione con la migrazione della piattaforma, sono in corso di distribuzione alcuni aggiornamenti secondari aggiuntivi:

- Saranno supportate altre versioni del sistema operativo. Vedere l'elenco [qui](security-center-faq.md#virtual-machines).
- L'elenco delle vulnerabilità del sistema operativo verrà esteso. Vedere l'elenco [qui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- I [prezzi](https://azure.microsoft.com/pricing/details/security-center/) verranno ripartiti su base oraria (in precedenza la ripartizione era su base giornaliera), con conseguente risparmio sui costi per alcuni clienti.
- La raccolta dei dati verrà richiesta e abilitata automaticamente per i clienti nel piano tariffario Standard.
- Il Centro sicurezza di Azure avvierà l'individuazione di soluzioni antimalware che non sono state distribuite tramite le estensioni di Azure. L'individuazione di Symantec Endpoint Protection e Defender per Windows 2016 sarà disponibile per prima.
- I criteri di prevenzione e le notifiche sono configurabili solo a livello di *sottoscrizione*, mentre i prezzi possono ancora essere impostati a livello di *gruppo di risorse*.


