---
title: 'Azure AD Connect: Selezionare il tipo di installazione | Microsoft Docs'
description: Questo argomento descrive come selezionare il tipo di installazione da usare per Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173267"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Selezionare il tipo di installazione da usare per Azure AD Connect
Per Azure AD Connect sono disponibili due tipi di installazione: rapida e personalizzata. Questo argomento consente di decidere quale opzione usare durante l'installazione.

## <a name="express"></a>Express
L'installazione rapida è l'opzione più comune e viene usata per circa il 90% di tutte le nuove installazioni. È stata progettata per fornire una configurazione adatta per la maggior parte degli scenari dei clienti.

Richiede:

- Una singola foresta Active Directory locale.
- Un account di amministratore dell'organizzazione che può essere usato per l'installazione.
- Meno di 100.000 oggetti nell'istanza locale di Active Directory.

Offre:

- [Sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) da locale ad Azure AD per l'accesso Single Sign-On.
- Una configurazione che consente di sincronizzare [utenti, gruppi, contatti e computer Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Sincronizzazione di tutti gli oggetti idonei in tutti i domini e tutte le unità organizzative.
- [Aggiornamento automatico](how-to-connect-install-automatic-upgrade.md) abilitato per garantire che venga usata sempre la versione più recente.

Casi in cui è comunque possibile usare l'installazione rapida:

- Se non si vogliono sincronizzare tutte le unità organizzative, è comunque possibile usare l'installazione rapida deselezionando **Avvia il processo di sincronizzazione...*** nell'ultima pagina. Eseguire quindi di nuovo l'Installazione guidata, modificare le unità organizzative nelle [opzioni di configurazione](how-to-connect-installation-wizard.md#customize-synchronization-options) e abilitare la sincronizzazione pianificata.
- Si desidera abilitare una delle funzionalità in Azure AD Premium, ad esempio il writeback delle password. Completare innanzitutto l'intera procedura di installazione rapida iniziale. Eseguire quindi di nuovo l'Installazione guidata e modificare le [opzioni di configurazione](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Personalizzate
Il percorso personalizzato offre molte più opzioni rispetto a quello rapido e deve essere usato in tutti i casi in cui l'organizzazione non dispone della configurazione indicata nella sezione precedente per l'installazione rapida.

Usare questo tipo nei seguenti casi:

- Non si ha accesso a un account amministratore dell'organizzazione in Active Directory.
- Si dispone di più foreste o si prevede di sincronizzare più foreste in futuro.
- Alcuni domini nella foresta non sono raggiungibili dal server Connect.
- Si prevede di usare la federazione o l'autenticazione pass-through per l'accesso utente.
- Si hanno più di 100.000 oggetti ed è necessario usare un'istanza completa di SQL Server.
- Si prevede di usare i filtri in base al gruppo oltre a quelli in base a dominio o unità organizzativa.

## <a name="upgrade-from-dirsync"></a>Aggiornamento da DirSync
Se si usa DirSync, seguire la procedura in [Eseguire l'aggiornamento da DirSync](how-to-dirsync-upgrade-get-started.md) per aggiornare la configurazione esistente. Sono disponibili due diverse opzioni di aggiornamento:

- Aggiornamento sul posto, per installare Connect sullo stesso server.
- Distribuzione parallela, per installare Connect in un nuovo server mentre il server DirSync esistente è ancora operativo.

## <a name="upgrade-from-azure-ad-sync"></a>Aggiornamento da Azure AD Sync
Se si usa Azure AD Sync, è possibile seguire la [stessa procedura](how-to-upgrade-previous-version.md) usata per eseguire l'aggiornamento a una versione più recente di Connect. Sono disponibili due diverse opzioni di aggiornamento:

- Aggiornamento sul posto, per installare Connect sullo stesso server.
- Migrazione swing, per installare Connect in un nuovo server mentre il server Azure AD Sync esistente è ancora operativo.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrazione da FIM2010 o MIM2016
Se si usa Forefront Identity Manager 2010 o Microsoft Identity Manager 2016 con Azure AD Connector, l'unica opzione disponibile è una migrazione. Seguire la procedura descritta in [swing-migration (Migrazione Swing)](how-to-upgrade-previous-version.md#swing-migration). Durante i passaggi, sostituire tutti i riferimenti ad Azure AD Sync con FIM2010/MIM2016.

## <a name="next-steps"></a>Passaggi successivi
In base all'opzione scelta, usare i contenuti a sinistra per trovare l'articolo con i passaggi dettagliati.
