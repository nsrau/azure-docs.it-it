---
title: Accesso all'ID di una VM
description: Descrive l'accesso e l'uso dell'ID univoco di una VM di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss

---
# Accesso e uso dell'ID univoco di una VM di Azure
L'ID univoco di una VM di Azure è un identificatore a 128 bit codificato e archiviato nel sistema SMBIOS di tutte le VM IaaS di Azure e attualmente può essere letto usando i comandi BIOS della piattaforma.

L'ID univoco di una VM di Azure è una proprietà di sola lettura. L'ID univoco di una VM di Azure non verrà modificato in caso di riavvio, arresto (pianificato o non pianificato), avvio/arresto della deallocazione, riparazione di servizi o ripristino sul posto. Se, tuttavia, la VM è uno snapshot e viene copiata per creare una nuova istanza, viene configurato l'ID univoco della VM di Azure.

> [!NOTE]
> Se in precedenza sono state create VM in esecuzione da quando questa nuova funzionalità è stata implementata (18 settembre 2014), riavviare ogni VM per ottenere automaticamente un ID univoco di Azure.
> 
> 

Per accedere all'ID univoco di una VM di Azure dalla VM:

## Creare una macchina virtuale
Per altre informazioni, vedere [Creare una macchina virtuale](virtual-machines-linux-creation-choices.md).

## Connettersi alla VM
Per altre informazioni, vedere [SSH da Linux](virtual-machines-linux-mac-create-ssh-keys.md)

## Effettuare una query dell'ID univoco di una VM
Comando (l'esempio usa **Ubuntu**):

    sudo dmidecode | grep UUID

Risultati previsti per l'esempio:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A

A causa dell'ordinamento dei bit big endian, l'ID univoco effettivo della VM in questo caso sarà:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A


L'ID univoco di una VM di Azure può essere usato in scenari diversi indipendentemente dal fatto che la VM sia in esecuzione in Azure o in locale e consente di soddisfare eventuali requisiti relativi a licenze, creazione di report o verifica generale nelle distribuzioni IaaS di Azure. Diversi fornitori di software indipendenti che compilano applicazioni e le certificano in Azure potrebbero richiedere di identificare una VM di Azure durante il suo ciclo di vita e di indicare se la VM è in esecuzione in Azure, in locale o in altri provider di servizi cloud. Questo identificatore di piattaforma consente, ad esempio, di rilevare se la licenza del software è corretta o di correlare i dati di una VM all'origine, ad esempio per fornire assistenza nell'impostazione della metrica appropriata a seconda della piattaforma e per tenere traccia di questa metrica e correlarla tra gli altri utenti.

<!---HONumber=AcomDC_0824_2016-->