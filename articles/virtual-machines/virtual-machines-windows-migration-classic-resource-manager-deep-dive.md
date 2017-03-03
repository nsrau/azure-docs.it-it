---
title: Migrazione tra i modelli di distribuzione per le macchine virtuali Windows in Azure | Microsoft Docs
description: Questo articolo propone un approfondimento tecnico sulla migrazione supportata dalla piattaforma di risorse dal modello di distribuzione classica ad Azure Resource Manager
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: d835d5825268a4ec0fa5b761f9b5714e3236b0ce
ms.openlocfilehash: 2dd35a65d1b5b6db1401cdf5737b5355aa6d564b
ms.lasthandoff: 01/31/2017


---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager
Sono descritte informazioni approfondite sulla migrazione del modello di distribuzione classica di Azure al modello di distribuzione di Azure Resource Manager. Le risorse vengono esaminate a livello di risorsa e di funzionalità per aiutare a comprendere come la piattaforma Azure esegue la migrazione di risorse tra i due modelli di distribuzione. Per altre informazioni, leggere l'articolo relativo all'annuncio del servizio, [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## <a name="detailed-guidance-on-migration"></a>Linee guida dettagliate sulla migrazione
Nella tabella seguente è disponibile la rappresentazione classica e di Resource Manager delle risorse. Le seguenti funzionalità e risorse non sono attualmente supportate.

| Rappresentazione classica | Rappresentazione in Resource Manager | Note dettagliate |
| --- | --- | --- |
| Nome del servizio cloud |Nome DNS |Durante la migrazione, viene creato un nuovo gruppo di risorse per ogni servizio cloud con il modello di denominazione `<cloudservicename>-migrated`. Questo gruppo di risorse contiene tutte le risorse. Il nome del servizio cloud diventa un nome DNS associato all'indirizzo IP pubblico. |
| Macchina virtuale |Macchina virtuale |Le proprietà specifiche della VM vengono migrate senza variazioni. Alcune informazioni su osProfile, come il nome del computer, non sono state archiviate nel modello di distribuzione classica e quindi rimarranno vuote dopo la migrazione. |
| Risorse del disco collegate alla VM |Dischi impliciti collegati alla VM |I dischi non vengono modellati come risorse di primo livello nel modello di distribuzione di Resource Manager. Ne verrà eseguita la migrazione come dischi impliciti della VM. Attualmente sono supportati solo i dischi collegati a una VM. Le VM di Resource Manager ora possono usare gli account di archiviazione classica, che consente di eseguire facilmente la migrazione dei dischi senza aggiornamenti. |
| Estensioni di VM |Estensioni di VM |Viene eseguita la migrazione dal modello di distribuzione classica di tutte le estensioni di risorse, a eccezione delle estensioni XML. |
| Certificati di macchine virtuali |Certificati nell'insieme di credenziali delle chiavi di Azure |Se un servizio cloud contiene i certificati di servizio, viene creato un nuovo insieme di credenziali delle chiavi di Azure per ogni servizio cloud e vengono spostati i certificati nell'insieme di credenziali delle chiavi. Le VM vengono aggiornate per fare riferimento ai certificati dell'insieme di credenziali delle chiavi. <br><br> **NOTA:** non eliminare l'insieme di credenziali poiché la macchina virtuale potrebbe entrare in uno stato di errore. È in corso un tentativo di miglioramento degli elementi back-end affinché gli insiemi di credenziali delle chiavi possano essere eliminati in maniera sicura o spostati insieme alla macchina virtuale in una nuova sottoscrizione. |
| Configurazione di WinRM |Configurazione di WinRM in osProfile |La configurazione di Gestione remota Windows viene trasferita senza variazioni, come parte della migrazione. |
| Proprietà del set di disponibilità |Risorsa del set di disponibilità |La specifica del set di disponibilità è una proprietà della VM nel modello di distribuzione classica. I set di disponibilità diventano una risorsa di primo livello come parte della migrazione. Le seguenti configurazioni non sono più supportate: più set di disponibilità per ogni servizio cloud o uno o più set di disponibilità insieme a VM non incluse in un set di disponibilità in un servizio cloud. |
| Configurazione di rete in una VM |Interfaccia di rete primaria |La configurazione di rete in una VM viene rappresentata come risorsa di interfaccia di rete primaria dopo la migrazione. Per le VM che non sono in una rete virtuale, l'indirizzo IP interno viene modificato durante la migrazione. |
| Più interfacce di rete in una VM |Interfacce di rete |Se una VM ha più interfacce di rete associate, ognuna diventerà una risorsa di primo livello come parte della migrazione nel modello di distribuzione di Resource Manager insieme a tutte le proprietà. |
| Set di endpoint con carico bilanciato |Bilanciamento del carico |Nel modello di distribuzione classica la piattaforma assegna un servizio di bilanciamento del carico implicito per ogni servizio cloud. Durante la migrazione, si crea una nuova risorsa di bilanciamento del carico e il set di endpoint di bilanciamento del carico diventa una serie di regole di bilanciamento del carico. |
| Regole NAT in ingresso |Regole NAT in ingresso |Gli endpoint di input definiti nella VM vengono convertiti in regole NAT in ingresso nel servizio di bilanciamento del carico durante la migrazione. |
| Indirizzo VIP |Indirizzo IP pubblico con nome DNS |L'indirizzo IP virtuale diventa un indirizzo IP pubblico e viene associato al bilanciamento del carico. |
| Rete virtuale |Rete virtuale |Viene eseguita la migrazione della rete virtuale con tutte le relative proprietà nel modello di distribuzione di Resource Manager. Viene creato un nuovo gruppo di risorse con il nome `-migrated`. Esistono [configurazioni non supportate](virtual-machines-windows-migration-classic-resource-manager.md). |
| IP riservati |Indirizzo IP pubblico con allocationMethod statico |Gli indirizzi IP riservati associati al servizio di bilanciamento del carico vengono trasferiti con la migrazione del servizio cloud o della macchina virtuale. La migrazione di indirizzi IP riservati non associati non è attualmente supportata. |
| Indirizzo IP pubblico per VM |Indirizzo IP pubblico con Allocationmethod dinamico |L'indirizzo IP pubblico associato alla VM viene convertito come risorsa indirizzo IP pubblico con il metodo di allocazione impostato su statico. |
| Gruppi di sicurezza di rete |Gruppi di sicurezza di rete |I gruppi di sicurezza di rete associati a una subnet vengono clonati come parte della migrazione nel modello di distribuzione di Resource Manager. Il gruppo di sicurezza di rete nel modello di distribuzione classica non viene rimosso durante la migrazione. Tuttavia, le operazioni del piano di gestione per il gruppo di sicurezza di rete vengono bloccate mentre la migrazione è in corso. |
| Server DNS |Server DNS |Viene eseguita la migrazione dei server DNS associati a una rete virtuale o alla VM come parte della migrazione delle risorse corrispondenti insieme a tutte le proprietà. |
| Route definite dall'utente |Route definite dall'utente |Le route definite dall'utente associate a una subnet vengono clonate come parte della migrazione al modello di distribuzione di Resource Manager. Il routing definito dall'utente nel modello di distribuzione classica non viene rimosso durante la migrazione. Le operazioni del piano di gestione per il routing definito dall'utente vengono bloccate mentre la migrazione è in corso. |
| Proprietà di inoltro IP nella configurazione di rete di una VM |Proprietà di inoltro IP nella scheda di rete |La proprietà di IP in una VM viene convertita in una proprietà nell'interfaccia di rete durante la migrazione. |
| Servizio di bilanciamento del carico con più IP |Servizio di bilanciamento del carico con più risorse IP pubblico |Ogni indirizzo IP pubblico associato al servizio di bilanciamento del carico viene convertito in una risorsa IP pubblico e associato al servizio di bilanciamento del carico al termine della migrazione. |
| Nomi DNS interni nella VM |Nomi DNS interni nella NIC |Durante la migrazione, i suffissi DNS interni per le VM vengono migrati a una proprietà di sola lettura chiamata "InternalDomainNameSuffix" sulla scheda di interfaccia di rete. Il suffisso non subisce modifiche dopo la migrazione e la risoluzione della VM continuerà a funzionare come in precedenza. |
| Gateway di rete virtuale |Gateway di rete virtuale |Le proprietà del gateway di rete virtuali restano invariate durante la migrazione, così come pure l'indirizzo VIP associato al gateway. |
| Sito di rete locale |Gateway di rete locale |Le proprietà del sito di rete locale vengono migrate senza modifiche in una nuova risorsa denominata Gateway di rete locale, che rappresenta i prefissi degli indirizzi locali e l'IP del gateway remoto. |
| Riferimenti alle connessioni |Connessione |I riferimenti alla connettività tra il gateway e il sito di rete locale nella configurazione di rete sono rappresentati da una risorsa appena creata e denominata Connessione in Resource Manager dopo la migrazione. Tutte le proprietà di riferimento alla connettività nei file di configurazione di rete vengono copiate senza modifiche nella risorsa Connessione appena creata. La connettività da VNet a Vnet nella distribuzione classica viene ottenuta creando due tunnel IPsec nei siti di rete locale che rappresentano le VNet. A questo punto viene eseguita la trasformazione nel tipo di connessione da VNet a Vnet nel modello di Resource Manager senza dover ricorrere ai gateway di rete locale. |

## <a name="illustration-of-a-simple-migration-walkthrough"></a>Illustrazione di una procedura dettagliata di migrazione semplice
Negli screenshot di esempio seguenti è mostrato un servizio cloud esistente con una VM (non in una rete virtuale) dopo la fase di preparazione:

![Rappresentazione classica dopo la preparazione](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)

Al termine del processo di migrazione, le catture di schermata seguenti mostrano che sono state create le nuove risorse in un nuovo gruppo di risorse: ![rappresentazione di Resource Manager dopo la preparazione](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere compreso i concetti fondamentali della migrazione di risorse IaaS classiche in Resource Manager, è possibile avviare la migrazione delle risorse.

* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
* [Migrazione supportata dalla piattaforma di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
* [Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell della community](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rivedere gli errori di migrazione più comuni](virtual-machines-migration-errors.md)


