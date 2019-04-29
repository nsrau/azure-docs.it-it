---
title: Monitorare identità e accesso nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare le funzionalità per identità e accesso nel Centro sicurezza di Azure per monitorare l'attività di accesso degli utenti e i problemi correlati all'identità.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7cb8d494d32f2a9c192418bfab2d5ca9b679c5fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704929"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorare identità e accesso nel Centro sicurezza di Azure (anteprima)
Questo articolo illustra come usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso degli utenti.

> [!NOTE]
> La funzionalità di monitoraggio dell'identità e dell'accesso è disponibile in anteprima e solo per il livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>

L'identità deve essere il piano di controllo per l'organizzazione e alla protezione dell'identità deve essere attribuita la massima priorità. Il perimetro di sicurezza si è evoluto da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. Attualmente, con il trasferimento nel cloud di una maggiore quantità di dati e app, l'identità diventa il nuovo perimetro.

Il monitoraggio delle attività associate alle identità consente di intraprendere azioni proattive prima che si verifichi un evento imprevisto o azioni reattive per bloccare un tentativo di attacco. Il dashboard Identità e accesso visualizza consigli, ad esempio:

- Abilitare MFA per gli account con privilegi nella sottoscrizione
- Rimuovere gli account esterni con autorizzazioni di scrittura dalla sottoscrizione
- Rimuovere account esterni con privilegi dalla sottoscrizione

> [!NOTE]
> Se la sottoscrizione include più di 600 account, il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per la sottoscrizione. I consigli non applicati sono elencati nella sezione "Valutazioni non disponibili", descritta di seguito.
Il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per gli agenti di amministrazione di un partner Cloud Solution Provider (CSP).
>
>

Vedere [Consigli](security-center-identity-access.md#recommendations) per l'elenco dei consigli relativi a Identità e accesso offerti dal Centro sicurezza.

## <a name="monitoring-security-health"></a>Monitoraggio dello stato di sicurezza
È possibile monitorare lo stato di sicurezza delle risorse nel dashboard **Centro sicurezza - Panoramica**. La sezione **Risorse** è un indicatore di integrità che indica il livello di gravità per ogni tipo di risorsa.

È possibile visualizzare l'elenco di tutti i problemi selezionando **Consigli**. In **Risorse** è possibile visualizzare un elenco di problemi specifici relativi a risorse di calcolo e app, sicurezza dei dati, rete, identità e accesso. Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

Per l'elenco completo dei consigli relativi a Identità e accesso, vedere [Consigli](security-center-identity-access.md#recommendations).

Per continuare, selezionare **Identità e accesso** in **Risorse** o dal menu principale del Centro sicurezza.

![Dashboard del Centro sicurezza][1]

## <a name="monitor-identity-and-access"></a>Monitorare identità e accesso
In **Identità e accesso** sono presenti due schede:

- **Panoramica**: consigli identificati dal Centro sicurezza.
- **Sottoscrizioni**: elenco delle sottoscrizioni e stato di sicurezza corrente di ognuna.

![Identità e accesso][2]

### <a name="overview-section"></a>Sezione Panoramica
In **Panoramica** è presente un elenco di consigli. La prima colonna elenca la raccomandazione. La seconda colonna visualizza il numero totale di sottoscrizioni interessate dal consiglio. La terza colonna indica la gravità del problema.

1. Selezionare un consiglio. Viene aperta la finestra del consiglio, che visualizza:

   - Descrizione del consiglio
   - Elenco delle sottoscrizioni non integre e integre
   - Elenco di risorse non analizzate a causa di una valutazione non riuscita o del fatto che la risorsa è all'interno di una sottoscrizione in esecuzione a livello gratuito e non viene quindi valutata

   ![Finestra del consiglio][3]

1. Per altri dettagli, selezionare una sottoscrizione nell'elenco.

### <a name="subscriptions-section"></a>Sezione Sottoscrizioni
In **Sottoscrizioni** è presente un elenco di sottoscrizioni. La prima colonna elenca le sottoscrizioni. La seconda colonna visualizza il numero totale di consigli per ogni sottoscrizione. La terza colonna indica il livello di gravità dei problemi.

![Scheda Sottoscrizioni][4]

1. Selezionare una sottoscrizione. Viene aperta una visualizzazione di riepilogo con tre schede:

   - **Consigli**: in base alle valutazioni non riuscite eseguite dal Centro sicurezza.
   - **Valutazioni superate**: elenco di valutazioni superate eseguite dal Centro sicurezza.
   - **Valutazioni non disponibili**: elenco di valutazioni la cui esecuzione non è riuscita a causa di un errore o perché la sottoscrizione ha più di 600 account.

   In **Consigli** è presente un elenco di consigli per la sottoscrizione selezionata, con l'indicazione del livello di gravità per ogni consiglio.

   ![Consigli per la sottoscrizione selezionata][5]

1. Selezionare un consiglio per una descrizione di questo, l'elenco delle sottoscrizioni non integre e integre e l'elenco delle risorse non analizzate.

   ![Descrizione del consiglio][6]

   In **Valutazioni superate** è presente un elenco di valutazioni superate.  Il livello di gravità di tali valutazioni è sempre di colore verde.

   ![Valutazioni superate][7]

1. Selezionare una valutazione superata dall'elenco per visualizzare la descrizione di questa e un elenco di sottoscrizioni integre. Per le sottoscrizioni non integre è presente una scheda che elenca tutte le sottoscrizioni non riuscite.

   ![Valutazioni superate][8]

## <a name="recommendations"></a>Consigli
Usare la tabella seguente come riferimento per comprendere i consigli relativi a Identità e accesso disponibili e gli effetti che producono se si decide di metterli in pratica.

|Tipo di risorsa|Punteggio di sicurezza|Recommendation|DESCRIZIONE|
|----|----|----|----|
|Sottoscrizione|50|Abilitare MFA per gli account dell'app di gestione di Microsoft Azure con autorizzazioni di proprietario per la sottoscrizione|Abilitare l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per tutti gli account della sottoscrizione con privilegi di amministratore per impedire una violazione degli account o delle risorse.|
|Sottoscrizione|50|Abilitare il Centro sicurezza le sottoscrizioni |Abilitare il Centro sicurezza in tutte le sottoscrizioni per il rilevamento delle minacce avanzato, JIT, l'inserimento delle applicazioni nell'elenco elementi consentiti e consigli avanzati |
|Sottoscrizione|50|Abilitare il livello standard del Centro sicurezza nelle sottoscrizioni |Abilitare il livello standard del Centro sicurezza in tutte le sottoscrizioni per il rilevamento delle minacce avanzato, JIT, l'inserimento delle applicazioni nell'elenco elementi consentiti e consigli avanzati.|
|Sottoscrizione|40|Abilitare MFA per gli account dell'app di gestione di Microsoft Azure con autorizzazioni di scrittura per la sottoscrizione|Abilitare l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per tutti gli account della sottoscrizione con privilegi di scrittura per impedire una violazione degli account o delle risorse.|
|Sottoscrizione|30|Remove external accounts with owner permissions from your subscription (Rimuovere gli account esterni con autorizzazioni di proprietario dalla sottoscrizione)|Rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di proprietario in modo da evitare l'accesso non monitorato. |
|Sottoscrizione|30|Abilitare MFA per gli account dell'app di gestione di Microsoft Azure con autorizzazioni di lettura per la sottoscrizione|Abilitare l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per tutti gli account della sottoscrizione con privilegi di lettura per impedire una violazione degli account o delle risorse.|
|Sottoscrizione|25|Rimuovere gli account esterni con autorizzazioni di scrittura dalla sottoscrizione|Rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura in modo da evitare l'accesso non monitorato. |
|Sottoscrizione|20|Rimuovere gli account deprecati con autorizzazioni di proprietario dalla sottoscrizione|Rimuovere gli account deprecati con autorizzazioni di proprietario dalle sottoscrizioni.|
|Sottoscrizione|5|Rimuovere gli account deprecati dalla sottoscrizione|Rimuovere gli account deprecati dalle sottoscrizioni correnti per consentire l'accesso solo agli utenti connessi. |
|Sottoscrizione|5|Designare più di un proprietario per la sottoscrizione|Designare più di un proprietario di sottoscrizione per assicurare la ridondanza dell'accesso amministratore.|
|Sottoscrizione|5|Designare fino a 3 proprietari per la sottoscrizione|Designare meno di 3 proprietari di sottoscrizione in modo da ridurre la probabilità di violazione da parte di un proprietario compromesso.|
|Insieme di credenziali delle chiavi|5|Abilitare i log di diagnostica nell'insieme di credenziali delle chiavi|Abilitare i log e conservarli fino a un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Sottoscrizione|15|Rimuovere gli account esterni con autorizzazioni di lettura dalla sottoscrizione|Rimuovere dalla sottoscrizione gli account esterni con privilegi di lettura in modo da evitare l'accesso non monitorato.|
|Sottoscrizione|1|Specificare dettagli del contatto per la sicurezza|Specificare le informazioni di contatto per la sicurezza per ogni sottoscrizione. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono. Le informazioni vengono usate per contattare l'utente se il team della sicurezza rileva risorse compromesse|

> ![NOTA] Se è stato creato un criterio di accesso condizionale che richiede l'autenticazione a più fattori ma prevede esclusioni, la valutazione per i consigli sull'autenticazione a più fattori del Centro sicurezza considera i criteri come non conformi perché consentono ad alcuni utenti di accedere ad Azure senza l'autenticazione a più fattori.
>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

- [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
- [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
