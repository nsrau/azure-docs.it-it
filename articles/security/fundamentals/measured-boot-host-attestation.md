---
title: Attestazione di avvio e host con misurazione del firmware-sicurezza di Azure
description: Panoramica tecnica dell'avvio e dell'attestazione dell'host con misurazione del firmware di Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 73ae811c17a578cafc557b0cda9e98b101dd5c03
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557821"
---
# <a name="measured-boot-and-host-attestation"></a>Attestazione di avvio e host misurata
Questo articolo descrive il modo in cui Microsoft garantisce l'integrità e la sicurezza degli host con l'avvio e l'attestazione dell'host misurati.

## <a name="measured-boot"></a>Avvio misurato

Il [Trusted Platform Module](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) è un componente di controllo crittograficamente sicuro e a prova di manomissione con firmware fornito da terze parti attendibili. Il log di configurazione di avvio contiene misure concatenate all'hash registrate nei registri di configurazione della piattaforma (PCR) quando l'host ha eseguito l'ultima sequenza di bootstrap. Nella figura seguente viene illustrato questo processo di registrazione. L'aggiunta incrementale di una misurazione precedentemente completata all'hash della misura successiva e l'esecuzione dell'algoritmo hash nell'Unione consente di eseguire il concatenamento hash.

![Diagramma che mostra il concatenamento hash del servizio di attestazione host.](./media/measured-boot-host-attestation/hash-chaining.png)

L'attestazione viene eseguita quando un host fornisce una prova dello stato di configurazione utilizzando il relativo log di configurazione di avvio (TCGLog). La falsificazione di un log di avvio è difficile perché il TPM non espone i valori PCR diversi dalle operazioni di lettura ed estensione. Inoltre, le credenziali fornite dal servizio di attestazione host sono sealed in base a valori di PCR specifici. L'uso del concatenamento di hash rende inutilizzabile dal punto di vista computazionale o sbloccare le credenziali fuori banda.

## <a name="host-attestation-service"></a>Servizio di attestazione host

Il servizio di attestazione host è una misura preventiva che controlla se i computer host sono affidabili prima che siano autorizzati a interagire con i dati o i carichi di lavoro del cliente. Il servizio di attestazione host verifica la convalida di un'istruzione di conformità (verifica verificabile della conformità dell'host) inviata da ogni host rispetto a un criterio di attestazione (definizione dello stato protetto). L'integrità di questo sistema è garantita da una [radice di attendibilità](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) fornita da un TPM.

Il servizio di attestazione host è presente in ogni cluster di Azure all'interno di un ambiente di blocco specifico. L'ambiente bloccato include altri servizi gatekeeper che fanno parte del protocollo di bootstrap del computer host. Un'infrastruttura a chiave pubblica (PKI) funge da intermediario per convalidare la provenienza delle richieste di attestazione e come emittente di identità (in caso di esito positivo dell'attestazione host). Le credenziali di post-attestazione rilasciate all'host di attestazione sono sealed nella relativa identità. Solo l'host richiedente può sbloccare le credenziali e utilizzarle per ottenere le autorizzazioni incrementali. In questo modo si evitano attacchi di tipo man-in-the Middle e spoofing.

Se un host di Azure arriva dalla factory con un errore di configurazione della sicurezza o viene manomesso nel Data Center, il TCGLog contiene gli indicatori di compromissione segnalati dal servizio di attestazione dell'host alla successiva attestazione, causando un errore di attestazione. Gli errori di attestazione impediscono alla flotta di Azure di considerare attendibile l'host danneggiato. Questa prevenzione blocca in modo efficace tutte le comunicazioni verso e dall'host e attiva un flusso di lavoro di eventi imprevisti. Viene eseguita un'analisi dettagliata e una dettagliata analisi post-mortem per determinare le cause principali e tutte le potenziali indicazioni della compromissione. Si tratta solo dopo che l'analisi è stata completata e che un host è stato aggiornato e ha la possibilità di partecipare alla flotta di Azure e di assumere i carichi di lavoro dei clienti.

Di seguito è riportata un'architettura di alto livello del servizio di attestazione host:

![Diagramma che illustra l'architettura del servizio di attestazione dell'host.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Misure di attestazione

Di seguito sono riportati esempi delle numerose misure acquisite oggi.

### <a name="secure-boot-and-secure-boot-keys"></a>Avvio protetto e chiavi di avvio protette
Convalidando che il database di firma e i digest del database delle firme revocate siano corretti, il servizio di attestazione host garantisce che l'agente client ritenga attendibile il software appropriato. Convalidando le firme del database della chiave di registrazione della chiave pubblica e della chiave della piattaforma pubblica, il servizio di attestazione host conferma che solo le parti attendibili sono autorizzate a modificare le definizioni del software considerato attendibile. Infine, assicurandosi che l'avvio protetto sia attivo, il servizio di attestazione dell'host convalida le definizioni che vengono applicate.

### <a name="debug-controls"></a>Controlli di debug
I debugger sono strumenti avanzati per gli sviluppatori. Tuttavia, l'accesso illimitato alla memoria e ad altri comandi di debug potrebbe indebolire la protezione dei dati e l'integrità del sistema se vengono assegnati a una parte non attendibile. Il servizio di attestazione host garantisce che qualsiasi tipo di debug sia disabilitato all'avvio nei computer di produzione.

### <a name="code-integrity"></a>Integrità del codice
UEFI [Secure Boot](secure-boot.md) garantisce che durante la sequenza di avvio possa essere eseguito solo software di basso livello attendibile. Gli stessi controlli, tuttavia, devono essere applicati anche nell'ambiente di post-avvio ai driver e ad altri eseguibili con accesso in modalità kernel. A tal fine, viene usato un criterio di integrità del codice per definire quali driver, file binari e altri eseguibili sono considerati attendibili specificando firme valide e non valide. Questi criteri vengono applicati. Le violazioni del criterio generano avvisi per il team di risposta agli eventi imprevisti della sicurezza per l'analisi.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle operazioni da eseguire per garantire l'integrità e la sicurezza della piattaforma, vedere:

- [Sicurezza del firmware](firmware.md)
- [Avvio protetto](secure-boot.md)
- [Progetto Cerberus](project-cerberus.md)
- [Crittografia di dati inattivi](encryption-atrest.md)
- [Sicurezza hypervisor](hypervisor.md)