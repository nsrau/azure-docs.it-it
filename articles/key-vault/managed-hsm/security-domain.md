---
title: Informazioni sul dominio di sicurezza HSM gestito di Azure
description: Panoramica del dominio di sicurezza HSM gestito, un insieme di credenziali di base necessarie per il ripristino di un modulo di protezione hardware gestito
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997214"
---
# <a name="about-the-managed-hsm-security-domain"></a>Informazioni sul dominio di sicurezza HSM gestito

Il dominio di sicurezza HSM gestito è un insieme di credenziali di base necessarie per ripristinare un modulo di protezione hardware gestito in caso di emergenza. Il dominio di sicurezza viene generato nell'hardware del modulo HSM gestito e negli enclavi software del servizio e rappresenta la "proprietà" del modulo HSM.

Ogni modulo di protezione hardware gestito deve avere un dominio di sicurezza da usare. Quando si richiede un nuovo HSM gestito, questo viene sottoposto a provisioning, ma non viene attivato fino a quando non si Scarica il dominio di sicurezza. Quando si esegue il provisioning di un modulo di protezione hardware gestito, ma non lo stato, sono disponibili due modi per attivarlo:
- Il download del dominio di sicurezza è il metodo predefinito e consente di archiviare in modo sicuro il dominio di sicurezza da usare con un altro HSM gestito o per eseguire il ripristino in caso di emergenza totale.
- Caricare un dominio di sicurezza esistente già disponibile, che consente di creare più istanze di HSM gestite che condividono lo stesso dominio di sicurezza.

## <a name="download-your-security-domain"></a>Scaricare il dominio di sicurezza

Quando viene effettuato il provisioning di un modulo di protezione hardware gestito ma non attivato, il download del dominio di sicurezza acquisisce le credenziali principali necessarie per il ripristino da una perdita completa di tutti i componenti hardware. Per scaricare il dominio di sicurezza, è necessario creare almeno 3 coppie di chiavi RSA e inviare le chiavi pubbliche al servizio quando si richiede il download del dominio di sicurezza. È anche necessario specificare il numero minimo di chiavi necessarie (quorum) per decrittografare il dominio di sicurezza in futuro. Il modulo HSM gestito Inizializza il dominio di sicurezza e lo crittografa con le chiavi pubbliche fornite usando l' [algoritmo di condivisione segreta di Shamir](https://dl.acm.org/doi/10.1145/359168.359176). Il BLOB del dominio di sicurezza scaricato può essere decrittografato solo quando sono disponibili almeno un quorum di chiavi private. è necessario mantenere le chiavi private sicure per garantire la sicurezza del dominio di sicurezza. Una volta completato il download, il modulo di protezione hardware gestito si troverà nello stato attivato pronto per l'uso.  

> [!IMPORTANT]
> Per un ripristino di emergenza completo, è necessario avere il dominio di sicurezza e il quorum delle chiavi private usate per proteggerlo e un backup completo del modulo di protezione hardware. Se si perde il dominio di sicurezza o sono sufficienti le chiavi RSA (chiave privata) per perdere il quorum e non sono presenti istanze in esecuzione del modulo HSM gestito, il ripristino di emergenza non sarà possibile.

## <a name="upload-a-security-domain"></a>Caricare un dominio di sicurezza

Quando viene effettuato il provisioning di un modulo di protezione hardware gestito ma non attivato, è possibile avviare un processo di ripristino del dominio di sicurezza. Il modulo di protezione hardware gestito genererà una coppia di chiavi RSA e restituirà la chiave pubblica. Quindi, è possibile caricare il dominio di sicurezza nel modulo di protezione hardware gestito. Prima del caricamento, il client (interfaccia della riga di comando di Azure o PowerShell) dovrà essere fornito con il numero minimo di chiavi private usate durante il download del dominio di sicurezza. Il client determinerà la decrittografia del dominio di sicurezza utilizzando questo quorum e la nuova crittografia utilizzando la chiave pubblica scaricata quando è stato richiesto il ripristino. Al termine del caricamento, il modulo di protezione hardware gestito si troverà nello stato attivato.

## <a name="backup-and-restore-behavior"></a>Comportamento di backup e ripristino

Il ripristino dei backup (backup completo o backup a chiave singola) può essere eseguito solo se il modulo di protezione hardware gestito dall'origine (in cui è stato creato il backup) e il modulo di protezione hardware gestito di destinazione (in cui verrà ripristinato il backup) condividono lo stesso dominio di sicurezza. In questo modo, un dominio di sicurezza definisce anche un limite crittografico per ogni modulo di protezione hardware gestito.

## <a name="next-steps"></a>Passaggi successivi

- Leggere una [panoramica del modulo di protezione hardware gestito](overview.md)
- Informazioni sulla [gestione di HSM gestiti con l'interfaccia](key-management.md) della riga di comando
- Vedere le [procedure consigliate per il modulo di protezione hardware gestito](best-practices.md)
