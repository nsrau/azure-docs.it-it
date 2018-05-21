---
title: Esempi di modelli di criteri | Microsoft Docs
description: Esempi di modelli di criteri di Azure per la rete virtuale.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Esempi di modelli di criteri di Azure per la rete virtuale

La tabella seguente include i collegamenti agli esempi di modelli di [Criteri di Azure](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Gli esempi sono riportati nel [repository degli esempi dei Criteri di Azure](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Rete**||
| [NSG X in ogni scheda di interfaccia di rete](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che con ogni interfaccia di rete virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [NSG X in ogni subnet](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che con ogni subnet virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [Nessuna tabella di route](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Impedisce che le reti virtuali vengano distribuite con una tabella di route. |
| [Usare una subnet approvata per le interfacce di rete delle macchine virtuali](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che le interfacce di rete usino una subnet approvata. Si specifica l'ID della subnet approvata. |
| [Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che le interfacce di rete usino una rete virtuale approvata. Si specifica l'ID della rete virtuale approvata. |
|**Monitoraggio**||
| [Audit diagnostic setting](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Controllare l'impostazione di diagnostica) | Controlla se le impostazioni di diagnostica non sono abilitate per i tipi di risorsa specificati. Si specifica una matrice di tipi di risorse per controllare se le impostazioni di diagnostica sono abilitate. |
|**Convenzioni di nome e il testo**||
| [Allow multiple name patterns](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Consenti più modelli di nomi) | Consente l'uso di uno dei numerosi modelli di nome per le risorse. |
| [Require like pattern](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Richiedi modello like) | Assicura che i nomi delle risorse soddisfano la condizione *like* per un modello. |
| [Require match pattern](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Richiedi modello corrispondente) | Assicura che i nomi delle risorse corrispondono al modello di denominazione specificato. |
| [Require tag match pattern](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Richiedi modello corrispondenza tag) | Assicura che un valore del tag corrisponde a un modello di testo. |
|**Tag**||
| [Iniziativa relativa ai criteri dei tag di fatturazione](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede i valori dei tag specificati per il centro di costo e il nome del prodotto. Usa i criteri predefiniti per applicare e imporre i tag obbligatori. Si specificano i valori obbligatori per i tag.  |
| [Imporre un tag e il relativo valore nei gruppi di risorse](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede un tag e un valore in un gruppo di risorse. Si specificano il nome del tag e il valore obbligatori.  |
| [Imporre un tag e il relativo valore](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede un nome e un valore di tag specificati. Si specificano il nome del tag e il valore da applicare.  |
| [Apply tag and its default value](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Applicare il tag e il valore predefinito) | Aggiunge un nome e un valore di tag specificati, se non è stato fornito il tag. Si specificano il nome e il valore del tag da applicare.  |
|**Generale**||
| [Località consentite](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che tutte le risorse vengano distribuite nelle località approvate. Si specifica una matrice di località approvate.  |
| [Tipi di risorse consentiti](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garantisce che solo i tipi di risorse approvati vengano distribuiti. Si specifica una matrice di tipi di risorse consentiti.  |
| [Tipi di risorse non consentiti](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Impedisce la distribuzione dei tipi di risorse specificati. Si specifica una matrice dei tipi di risorse da bloccare.  |