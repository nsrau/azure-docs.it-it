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
ms.openlocfilehash: b0b3b65ee2360a5cec32235f2ee5bf8d4839cc8b
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342081"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Esempi di modelli di criteri di Azure per la rete virtuale

La tabella seguente include collegamenti a esempi di [criteri di Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Gli esempi sono riportati nel [repository degli esempi dei Criteri di Azure](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Rete**||
| [NSG X in ogni scheda di interfaccia di rete](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che con ogni interfaccia di rete virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [NSG X in ogni subnet](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che con ogni subnet virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [Nessuna tabella di route](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Impedisce che le reti virtuali vengano distribuite con una tabella di route. |
| [Usare una subnet approvata per le interfacce di rete delle macchine virtuali](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che le interfacce di rete usino una subnet approvata. Si specifica l'ID della subnet approvata. |
| [Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che le interfacce di rete usino una rete virtuale approvata. Si specifica l'ID della rete virtuale approvata. |
|**Monitoraggio**||
| [Audit diagnostic setting](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Controllare l'impostazione di diagnostica) | Controlla se le impostazioni di diagnostica non sono abilitate per i tipi di risorsa specificati. Si specifica una matrice di tipi di risorse per controllare se le impostazioni di diagnostica sono abilitate. |
|**Convenzioni di nome e il testo**||
| [Allow multiple name patterns](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Consenti più modelli di nomi) | Consente l'uso di uno dei numerosi modelli di nome per le risorse. |
| [Require like pattern](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Richiedi modello like) | Assicura che i nomi delle risorse soddisfano la condizione *like* per un modello. |
| [Require match pattern](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Richiedi modello corrispondente) | Assicura che i nomi delle risorse corrispondono al modello di denominazione specificato. |
| [Require tag match pattern](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Richiedi modello corrispondenza tag) | Assicura che un valore del tag corrisponde a un modello di testo. |
|**Tag**||
| [Iniziativa relativa ai criteri dei tag di fatturazione](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede i valori dei tag specificati per il centro di costo e il nome del prodotto. Usa i criteri predefiniti per applicare e imporre i tag obbligatori. Si specificano i valori obbligatori per i tag.  |
| [Imporre un tag e il relativo valore nei gruppi di risorse](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede un tag e un valore in un gruppo di risorse. Si specificano il nome del tag e il valore obbligatori.  |
| [Imporre un tag e il relativo valore](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede un nome e un valore di tag specificati. Si specificano il nome del tag e il valore da applicare.  |
| [Apply tag and its default value](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Applicare il tag e il valore predefinito) | Aggiunge un nome e un valore di tag specificati, se non è stato fornito il tag. Si specificano il nome e il valore del tag da applicare.  |
|**Generale**||
| [Località consentite](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Richiede che tutte le risorse vengano distribuite nelle località approvate. Si specifica una matrice di località approvate.  |
| [Tipi di risorse consentiti](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garantisce che solo i tipi di risorse approvati vengano distribuiti. Si specifica una matrice di tipi di risorse consentiti.  |
| [Tipi di risorse non consentiti](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Impedisce la distribuzione dei tipi di risorse specificati. Si specifica una matrice dei tipi di risorse da bloccare.  |