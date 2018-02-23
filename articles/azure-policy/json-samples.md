---
title: Esempi di modelli di criteri | Microsoft Docs
description: Esempi JSON per Criteri di Azure
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 01/17/2018
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 19641f3b1f2781cf23955743bf65172ff84fb544
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="templates-for-azure-policy"></a>Modelli per Criteri di Azure

La tabella seguente include i collegamenti ai modelli json per Criteri di Azure. Questi esempi sono riportati nel [repository degli esempi dei Criteri di Azure](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Calcolo**||
| [Immagini delle macchine virtuali approvate](scripts/allowed-custom-images.md) | Richiede che solo le immagini personalizzate approvate vengano distribuite nell'ambiente in uso. Si specifica una matrice di ID di immagini approvate. |
| [Controllare quando la macchina virtuale non usa un disco gestito](scripts/create-vm-managed-disk.md) | Controlla quando viene creata una macchina virtuale che non usa dischi gestiti.|
| [Controllare se l'estensione non esiste](scripts/audit-ext-not-exist.md) | Controlla se un'estensione non viene distribuita con una macchina virtuale. Si specifica il server di pubblicazione e il tipo dell'estensione per controllare se è stata distribuita. |
| [Allow custom VM image from a Resource Group](scripts/allow-custom-vm-image.md) (Consentire un'immagine di macchina virtuale personalizzata da un gruppo di risorse) |  Richiede che le immagini personalizzate provengano da un gruppo di risorse approvato. Si specifica il nome del gruppo di risorse approvato. |
| [Deny hybrid use benefit](scripts/deny-hybrid-use.md) (Rifiutare il vantaggio Hybrid Use) | Impedisce di usare il vantaggio Azure Hybrid Use. Usarlo quando non si vuole consentire l'uso di licenze locali. |
| [Estensioni di macchine virtuali non consentite](scripts/not-allowed-vm-ext.md) | Impedisce l'uso di estensioni specificate. Si specifica una matrice che contiene i tipi di estensioni non consentiti. |
| [Consentire solo una determinata immagine della piattaforma della macchina virtuale](scripts/allow-certain-vm-image.md) | Richiede che le macchine virtuali usino una versione specifica di UbuntuServer. |
| [Create VM using Managed Disk](scripts/use-managed-disk-vm.md) (Creare una macchina virtuale usando un disco gestito) | Richiede che le macchine virtuali usino dischi gestiti.|
|**Monitoraggio**||
| [Audit diagnostic setting](scripts/audit-diag-setting.md) (Controllare l'impostazione di diagnostica) | Controlla se le impostazioni di diagnostica non sono abilitate per i tipi di risorsa specificati. Si specifica una matrice di tipi di risorse per controllare se le impostazioni di diagnostica sono abilitate. |
|**Convenzioni di nome e il testo**||
| [Allow multiple name patterns](scripts/allow-multiple-name-patterns.md) (Consenti più modelli di nomi) | Consente l'uso di uno dei numerosi modelli di nome per le risorse. |
| [Require like pattern](scripts/enforce-like-pattern.md) (Richiedi modello like) | Assicura che i nomi delle risorse soddisfano la condizione "like" per un modello. |
| [Require match pattern](scripts/enforce-match-pattern.md) (Richiedi modello corrispondente) | Assicura che i nomi delle risorse corrispondono al modello di denominazione. |
| [Require tag match pattern](scripts/enforce-tag-match-pattern.md) (Richiedi modello corrispondenza tag) | Assicura che un valore del tag corrisponde a un modello di testo. |
|**Rete**||
| [Allowed Application Gateway SKUs](scripts/allowed-app-gate-sku.md) (SKU del gateway applicazione consentiti) | Richiede che i gateway applicazione usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [Controllare se Network Watcher non è abilitato per l'area](scripts/net-watch-not-enabled.md) | Controlla se Network Watcher non è abilitato per un'area specificata. Si specifica il nome dell'area per controllare se Network Watcher è abilitato. |
| [NSG X in ogni scheda di interfaccia di rete](scripts/nsg-on-nic.md) | Richiede che con ogni interfaccia di rete virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [NSG X in ogni subnet](scripts/nsg-on-subnet.md) | Richiede che con ogni subnet virtuale venga usato uno specifico gruppo di sicurezza di rete. Si specifica l'ID del gruppo di sicurezza di rete da usare. |
| [Larghezza di banda di Express Route consentita](scripts/allowed-er-band.md) | Richiede che le istanze di Express Route usino un set specificato di larghezze di banda. Si specifica una matrice di SKU che possono essere specificati per Express Route. |
| [Località di peering consentita per Express Route](scripts/allowed-peering-er.md) | Richiede che le istanze di Express Route usino località di peering specificate. Si specifica una matrice di località di peering consentite. |
| [SKU di Express Route consentiti](scripts/allowed-er-skus.md) | Richiede che le istanze di Express Route usino uno SKU approvato. Si specifica una matrice di SKU consentiti. |
| [SKU di bilanciamento del carico consentiti](scripts/allowed-lb-skus.md) | Richiede che i servizi di bilanciamento del carico usino uno SKU approvato. Si specifica una matrice di SKU consentiti. |
| [Nessun peering reti per la rete ER](scripts/no-peering-er-net.md) | Impedisce a un peering reti di essere associato a una rete in un gruppo di risorse specificato. Usarlo per impedire la connessione con l'infrastruttura di rete gestita centrale. Si specifica il nome del gruppo di risorse per impedire l'associazione. |
| [Nessuna tabella di route definita dall'utente](scripts/no-user-def-route-table.md)  |Impedisce che le reti virtuali vengano distribuite con una tabella di route definita dall'utente. |
| [SKU del gateway di rete virtuale consentiti](scripts/allowed-vn-gate-sku.md) | Richiede che i gateway di rete virtuale usino uno SKU e un tipo di gateway approvati. Si specifica una matrice di SKU approvati e una matrice di tipi di gateway approvati. |
| [Usare una subnet approvata per le interfacce di rete delle macchine virtuali](scripts/use-approved-subnet-vm-nics.md) | Richiede che le interfacce di rete usino una subnet approvata. Si specifica l'ID della subnet approvata. |
| [Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali](scripts/use-approved-vnet-vm-nics.md) | Richiede che le interfacce di rete usino una rete virtuale approvata. Si specifica l'ID della rete virtuale approvata. |
|**Tag**||
| [Iniziativa relativa ai criteri dei tag di fatturazione](scripts/billing-tags-policy-init.md) | Richiede i valori dei tag specificati per il centro di costo e il nome del prodotto. Usa i criteri predefiniti per applicare e imporre i tag obbligatori. Si specificano i valori obbligatori per i tag.  |
| [Imporre un tag e il relativo valore nei gruppi di risorse](scripts/enforce-tag-rg.md) | Richiede un tag e un valore in un gruppo di risorse. Si specificano il nome del tag e il valore obbligatori.  |
|**SQL**||
| [Controllare l'impostazione di controllo a livello di database SQL](scripts/audit-sql-db-audit-setting.md) | Controlla le impostazioni di controllo del database SQL se tali impostazioni non corrispondono a un'impostazione specificata. Si specifica un valore che indica se le impostazioni di controllo devono essere abilitate o disabilitate.  |
| [Audit transparent data encryption status](scripts/audit-trans-data-enc-status.md) (Controllare lo stato di Transparent Data Encryption) | Controlla Transparent Data Encryption del database SQL se non è abilitata.  |
| [Controllare l'impostazione di rilevamento delle minacce a livello di database](scripts/audit-db-threat-det-setting.md) | Controlla i criteri degli avvisi di sicurezza del database SQL se tali criteri non sono impostati sullo stato specificato. Si specifica un valore che indica se il rilevamento delle minacce è abilitato o disabilitato.  |
| [Controllare l'impostazione di controllo a livello di SQL Server](scripts/audit-sql-ser-leve-audit-setting.md) | Controlla le impostazioni di controllo di SQL server se tali impostazioni non corrispondono a un'impostazione specificata. Si specifica un valore che indica se le impostazioni di controllo devono essere abilitate o disabilitate. |
| [Controllare l'impostazione di rilevamento delle minacce a livello di server](scripts/audit-sql-ser-threat-det-setting.md) | Controlla i criteri degli avvisi di sicurezza del database SQL se tali criteri non sono impostati sullo stato specificato. Si specifica un valore che indica se il rilevamento delle minacce è abilitato o disabilitato.  |
| [Audit no Azure Active Directory administrator](scripts/audit-no-aad-admin.md) (Controlla se non è presente un amministratore di Azure Active Directory) | Controlla quando non vi è nessun amministratore di Azure Active Directory assegnato a SQL Server. |
| [Allowed SQL DB SKUs](scripts/allowed-sql-db-skus.md) (SKU del database SQL consentiti) | Richiede ai database SQL di usare uno SKU approvato. Si specifica una matrice di ID di SKU consentiti o una matrice di nomi di SKU consentiti. |
|**Archiviazione**||
| [SKU consentiti per account di archiviazione e macchine virtuali](scripts/allowed-skus-storage.md) | Richiede che gli account di archiviazione e le macchine virtuali usino SKU approvati. Usa i criteri predefiniti per garantire SKU approvati. Si specifica una matrice di SKU di macchine virtuali approvati e una matrice di SKU di account di archiviazione approvati. |
| [Assicurare solo traffico HTTPS per un account di archiviazione](scripts/ensure-https-stor-acct.md) | Richiede che gli account di archiviazione usino il traffico HTTPS.  |
| [Rifiutare il livello di accesso sporadico per gli account di archiviazione](scripts/deny-cool-access-tiering.md) | Impedisce l'uso del livello di accesso sporadico per gli account di archiviazione BLOB.  |
| [Assicurare la crittografia dei file di archiviazione](scripts/ensure-store-file-enc.md) | Richiede che la crittografia dei file sia abilitata per gli account di archiviazione.  |
|**Criteri predefiniti**||
| [Località consentite](scripts/allowed-locs.md) | Richiede che tutte le risorse vengano distribuite nelle località approvate. Si specifica una matrice di località approvate.  |
| [Tipi di risorse consentiti](scripts/allowed-res-types.md) | Garantisce che solo i tipi di risorse approvati vengano distribuiti. Si specifica una matrice di tipi di risorse consentiti.  |
| [SKU degli account di archiviazione consentiti](scripts/allowed-stor-acct-skus.md) | Richiede che gli account di archiviazione usino uno SKU approvato. Si specifica una matrice di SKU approvati. |
| [Apply tag and its default value](scripts/apply-tag-def-val.md) (Applicare il tag e il valore predefinito) | Aggiunge un nome e un valore di tag specificati, se non è stato fornito il tag. Si specificano il nome del tag e il valore da applicare.  |
| [Imporre un tag e il relativo valore](scripts/enforce-tag-val.md) | Richiede un nome e un valore di tag specificati. Si specificano il nome del tag e il valore da applicare.  |
| [Tipi di risorse non consentiti](scripts/not-allowed-res-type.md) | Impedisce la distribuzione dei tipi di risorse specificati. Si specifica una matrice dei tipi di risorse da bloccare.  |
| [Richiedere SQL Server versione 12.0](scripts/req-sql-12.md) | Richiede ai server SQL di usare la versione 12.0.  |
| [Richiedere la crittografia dell'account di archiviazione](scripts/req-store-acct-enc.md) | Richiede all'account di archiviazione di usare la crittografia BLOB.  |
