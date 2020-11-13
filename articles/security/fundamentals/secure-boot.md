---
title: 'Avvio protetto del firmware: sicurezza di Azure'
description: Panoramica tecnica dell'avvio protetto del firmware di Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557845"
---
# <a name="secure-boot"></a>Avvio protetto

L'avvio protetto è una funzionalità del [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) che richiede la verifica di tutti i componenti software e firmware di basso livello prima del caricamento. Durante l'avvio, l'avvio protetto UEFI controlla la firma di ogni componente software di avvio, inclusi i driver del firmware UEFI (noti anche come Option ROM), le applicazioni Extensible Firmware Interface (EFI) e i driver e i file binari del sistema operativo. Se le firme sono valide o considerate attendibili dall'OEM (Original Equipment Manufacturer), il computer viene avviato e il firmware fornisce il controllo al sistema operativo.

## <a name="components-and-process"></a>Componenti e processo

L'avvio protetto si basa su questi componenti critici:

- Chiave della piattaforma (PK): stabilisce la relazione di trust tra il proprietario della piattaforma (Microsoft) e il firmware. La metà pubblica è PKpub e la metà privata è PKpriv.
- Database chiave di registrazione chiave (KEK): stabilisce una relazione di trust tra il firmware del sistema operativo e la piattaforma. La metà pubblica è KEKpub e la metà privata è KEKpriv.
- Database di firma (DB): include i digest per i firmatari attendibili (chiavi pubbliche e certificati) del firmware e dei moduli di codice software autorizzati a interagire con il firmware della piattaforma.
- Revoked signatures database (dbx): contiene i digest revocati dei moduli di codice identificati come dannosi, vulnerabili, compromessi o non attendibili. Se un hash si trova nel database di firma e il database delle firme revocate, il database delle firme revocate prende la precedenza.

La figura e il processo seguenti illustrano l'aggiornamento di questi componenti:

![Diagramma che mostra i componenti di avvio protetti.](./media/secure-boot/secure-boot.png)

L'OEM archivia i digest di avvio protetti sulla RAM non volatile (NV-RAM) del computer al momento della produzione.

1. Il database di firma (DB) viene popolato con i firmatari o gli hash di immagine delle applicazioni UEFI, i caricatori del sistema operativo (ad esempio il caricatore del sistema operativo Microsoft o il Boot Manager) e i driver UEFI considerati attendibili.
2. Il database delle firme revocate (dbx) viene popolato con i digest dei moduli che non sono più attendibili.
3. Il database chiave di registrazione chiave (KEK) viene popolato con le chiavi di firma che possono essere utilizzate per aggiornare il database di firma e il database delle firme revocate. I database possono essere modificati tramite aggiornamenti firmati con la chiave corretta o tramite aggiornamenti da un utente autorizzato fisicamente presente utilizzando i menu del firmware.
4. Una volta aggiunti i database DB, dbx e KEK e completato il test e la convalida del firmware finale, l'OEM blocca la modifica del firmware e genera una chiave della piattaforma (PK). Il PK può essere usato per firmare gli aggiornamenti al KEK o per disattivare l'avvio protetto.

Durante ogni fase del processo di avvio, i digest di firmware, bootloader, sistema operativo, driver del kernel e altri artefatti della catena di avvio vengono calcolati e confrontati con valori accettabili. Il firmware e il software individuati come non attendibili non possono essere caricati. Pertanto, è possibile bloccare attacchi malware di basso livello o attacchi malware di pre-avvio.

## <a name="secure-boot-on-the-azure-fleet"></a>Avvio protetto nella flotta di Azure
Attualmente, ogni computer caricato e distribuito nella flotta di calcolo di Azure per ospitare i carichi di lavoro dei clienti deriva da piani Factory con avvio protetto abilitato. Gli strumenti e i processi di destinazione sono disponibili in ogni fase della pipeline di compilazione e integrazione dell'hardware per garantire che l'abilitazione dell'avvio protetto non venga ripristinata per errore o per finalità dannose.

Verifica che i digest di database e dbx siano corretti:

- Il bootloader è presente in una delle voci di database
- Firma del bootloader valida
- Avvii host con software attendibile

 Convalidando le firme di KEKpub e PKpub, è possibile verificare che solo le parti attendibili dispongano delle autorizzazioni per modificare le definizioni del software considerato attendibile. Infine, assicurandosi che l'avvio protetto sia attivo, è possibile verificare che queste definizioni siano applicate.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle operazioni da eseguire per garantire l'integrità e la sicurezza della piattaforma, vedere:

- [Sicurezza del firmware](firmware.md)
- [Attestazione di avvio e host misurata](measured-boot-host-attestation.md)
- [Progetto Cerberus](project-cerberus.md)
- [Crittografia di dati inattivi](encryption-atrest.md)
- [Sicurezza hypervisor](hypervisor.md)