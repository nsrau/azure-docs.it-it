---
title: "Progettare set di scalabilità di macchine virtuali per la scalabilità | Microsoft Docs"
description: "Informazioni su come progettare set di scalabilità di macchine virtuali per la scalabilità"
keywords: "macchina virtuale linux,set di scalabilità macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4821bf5b7e3527e9d130e24c81d90368f395d30


---
# <a name="designing-vm-scale-sets-for-scale"></a>Progettare set di scalabilità di VM per la scalabilità
Questo argomento descrive una serie di considerazioni di progettazione per i set di scalabilità di macchine virtuali. Per sapere che cosa sono i set di scalabilità di macchine virtuali, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

## <a name="storage"></a>Archiviazione
Un set di scalabilità usa account di archiviazione per archiviare i dischi del sistema operativo delle VM all'interno del set stesso. È consigliabile un rapporto di 20 VM al massimo per account di archiviazione. È anche consigliabile usare le diverse lettere dell'alfabeto come caratteri iniziali dei nomi degli account di archiviazione. Questo consente di suddividere il carico tra diversi sistemi interni. Ad esempio, nel modello seguente è usata la funzione di Modello di Resource Manager uniqueString per generare hash di prefisso anteposti a nomi di account di archiviazione: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).

## <a name="overprovisioning"></a>Provisioning eccessivo
A partire dalla versione dell'API "2016-03-30", l'impostazione predefinita dei set di scalabilità di VM corrisponde al provisioning eccessivo di VM. Con il provisioning eccessivo attivato, il set di scalabilità di fatto avvia un maggior numero di macchine virtuali rispetto a quanto richiesto, dopodiché elimina le macchine virtuali avviate per ultime. Il provisioning eccessivo migliora le percentuali di riuscita del provisioning. Le VM aggiuntive non vengono addebitate all'utente e non se ne tiene conto per il raggiungimento dei limiti di quota.

Il provisioning eccessivo consente di migliorare la percentuale di riuscita del provisioning, ma può causare il comportamento confuso delle applicazioni non progettate per gestire VM che scompaiono senza preavviso. Per disattivare la funzionalità di provisioning in eccesso, inserire la stringa "overprovision": "false" nel modello, se non è già presente. Per ulteriori informazioni, consultare la [documentazione dell'API REST relativo al set di scalabilità delle VM](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se si disattiva il provisioning eccessivo, è possibile usare senza problemi un numero maggiore di VM per account di archiviazione. Non è tuttavia consigliabile superare le 40 macchine virtuali.

## <a name="limits"></a>Limiti
Un set di scalabilità basato su un'immagine personalizzata creata dall'utente deve creare tutti i dischi rigidi virtuali del disco del sistema operativo all'interno di un unico account di archiviazione. Di conseguenza, il numero massimo consigliato di VM in un set di scalabilità basato su un'immagine personalizzata è 20. Se si disattiva il provisioning eccessivo, è possibile arrivare a 40.

Un set di scalabilità basato su un'immagine di piattaforma è attualmente limitato a 100 VM, per le quali è consigliabile usare 5 account di archiviazione.

Per un numero di macchine virtuali superiore a tali limiti, è necessario distribuire più set di scalabilità, come indicato in [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).




<!--HONumber=Nov16_HO3-->


