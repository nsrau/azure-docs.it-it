---
title: Pubblicare applicazioni in reti e posizioni separate tramite gruppi di connettori nel proxy di applicazione di Azure AD | Microsoft Docs
description: Illustra come creare e gestire i gruppi di connettori nel proxy di applicazione di Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 39049c7a1e2a4d61ef62bd06cda9ef1bb2c50c0b
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017

---

# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portale di Azure classico](active-directory-application-proxy-connectors.md)
>

## <a name="azure-ad-application-proxy-and-connector-groups"></a>Gruppi di connettori e proxy di applicazione di Azure AD

Il proxy di applicazione di Azure AD viene usato per un numero crescente di scenari e applicazioni dei clienti. Per questo motivo, il proxy di applicazione è stato reso ancora più flessibile grazie all'abilitazione di altre topologie. Ora è possibile creare gruppi di connettori del proxy di applicazione. Questa nuova funzionalità permette di assegnare connettori specifici per la gestione di determinate applicazioni. La funzionalità consente molti nuovi casi d'uso per il proxy di applicazione che prima non erano possibili. 

Il concetto di base è che ogni connettore del proxy di applicazione è assegnato a un gruppo di connettori. Tutti i connettori che appartengono allo stesso gruppo di connettori costituiscono un gruppo separato per la disponibilità elevata e il bilanciamento del carico. Per impostazione predefinita, tutti i connettori appartengono a un gruppo predefinito. L'amministratore può creare nuovi gruppi e modificare le assegnazioni nel portale di Azure. 

Per impostazione predefinita, tutte le applicazioni vengono assegnate a un gruppo di connettori predefinito. Se l'amministratore non apporta modifiche, il sistema continua a comportarsi come ha sempre fatto. Se non si apportano modifiche, tutte le applicazioni assegnate al gruppo di connettori predefinito includono tutti i connettori. Se però i connettori vengono organizzati in gruppi, è possibile impostare ogni applicazione per l'uso di un gruppo di connettori specifico. In tal caso, verranno usati su richiesta solo i connettori inclusi nel gruppo specifico.


>[!NOTE] 
>Dato che i nuovi connettori vengono assegnati automaticamente a un gruppo di connettori predefinito, per distribuzioni di grandi dimensioni è consigliabile che non ci siano applicazioni assegnate al gruppo predefinito. Dopo l'installazione, quindi, i nuovi connettori non riceveranno traffico attivo. Potranno iniziare a gestire il traffico attivo solo dopo l'assegnazione del connettore a uno dei gruppi attivi. Questo permette anche di impostare i connettori in una modalità inattiva per consentirne la manutenzione.
>

## <a name="prerequisite-create-your-connector-groups"></a>Prerequisito: creare i gruppi di connettori
Per raggruppare i connettori è necessario assicurarsi di avere [installato più connettori](active-directory-application-proxy-enable.md). Quando si installa un nuovo connettore, questo si aggiunge automaticamente al gruppo di connettori **predefinito** .

## <a name="step-1-create-connector-groups"></a>Passaggio 1: Creare gruppi di connettori
È possibile creare tutti i gruppi di connettori desiderati. La creazione di un gruppo di connettori viene eseguita nel [portale di Azure](https://portal.azure.com).

1. Selezionare **Azure Active Directory** per passare al dashboard di gestione per la directory. Da qui selezionare **Applicazioni aziendali** > **Proxy dell'applicazione**.
2. Selezionare il pulsante **Connector Groups** (Gruppi di connettori). Viene visualizzato il pannello New Connector Group (Nuovo gruppo di connettori).
3. Assegnare un nome al nuovo gruppo di connettori, quindi usare il menu a discesa per selezionare i connettori appartenenti a questo gruppo.
4. Selezionare **Salva** al termine dell'operazione.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Passaggio 2: Assegnare applicazioni ai gruppi di connettori
L'ultimo passaggio prevede l'assegnazione di ogni applicazione al gruppo di connettori da cui verrà gestita.

1. Nel dashboard di gestione per la directory selezionare **Applicazioni aziendali** > **All applications** (Tutte le applicazioni) > l'applicazione che si vuole assegnare a un gruppo di connettori > **Proxy dell'applicazione**.
2. In **Gruppo di connettori**usare il menu a discesa per selezionare il gruppo che si vuole venga usato dall'applicazione.
3. Fare clic su **Salva** per salvare la modifica.

## <a name="use-cases-for-connector-groups"></a>Casi d'uso per gruppi di connettori 

I gruppi di connettori sono utili in varie situazioni, ad esempio:

###<a name="sites-with-multiple-interconnected-datacenters"></a>Siti con più data center interconnessi

Molte organizzazioni hanno diversi data center interconnessi. In questo caso è preferibile mantenere la maggior quantità di traffico possibile all'interno del data center, perché i collegamenti tra data center sono lenti e dispendiosi. È possibile distribuire connettori in ogni data center per rendere disponibili solo le applicazioni che risiedono all'interno del data center. Questo approccio riduce al minimo i collegamenti tra data center e offre un'esperienza completamente trasparente agli utenti.

### <a name="applications-installed-on-isolated-networks"></a>Applicazioni installate in reti isolate

È possibile ospitare le applicazioni in reti che non fanno parte della rete aziendale principale. È possibile usare gruppi di connettori per installare connettori dedicati in reti isolate, in modo da isolare anche le applicazioni per la rete. Solitamente questo accade quando un fornitore di terze parti gestisce un'applicazione specifica per l'organizzazione. 

I gruppi di connettori permettono di installare connettori dedicati per le reti che pubblicano solo determinate applicazioni, rendendo più semplice e sicuro l'outsourcing della gestione delle applicazioni a fornitori di terze parti.

### <a name="applications-installed-on-iaas"></a>Applicazioni installate in IaaS 

Per le applicazioni installate in IaaS per l'accesso al cloud, i gruppi di connettori offrono un servizio comune per proteggere l'accesso a tutte le app. I gruppi di connettori non creano dipendenza aggiuntiva dalla rete aziendale o non frammentano l'esperienza delle app. I connettori possono essere installati in ogni data center nel cloud e gestire solo le applicazioni che si trovano nella rete. È possibile installare più connettori per ottenere una disponibilità elevata.

In questo caso, l'organizzazione ha diverse macchine virtuali connesse alla rete virtuale IaaS ospitata. Per permettere ai dipendenti di usare le applicazioni, tali reti private sono connesse alla rete aziendale tramite VPN da sito a sito. Questa soluzione offre un'esperienza ottimale ai dipendenti a livello locale. Potrebbe tuttavia non essere l'ideale per i dipendenti che lavorano in remoto, perché richiede un'infrastruttura locale aggiuntiva, come illustra il diagramma seguente:

![Rete IaaS di Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Questo può diventare un problema perché molte organizzazioni usano più fornitori cloud, dal momento che le applicazioni si trovano in diversi data center. Con i gruppi di connettori del proxy di applicazione di Azure AD, è possibile abilitare un servizio comune per proteggere l'accesso a tutte le applicazioni senza creare una dipendenza aggiuntiva nella rete aziendale:

![Più fornitori cloud IaaS per Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Scenario a più foreste: gruppi di connettori diversi per ogni foresta

La maggior parte dei clienti che hanno distribuito il proxy di applicazione usa le funzionalità Single Sign-On (SSO) eseguendo la delega vincolata Kerberos (KCD). A tale scopo, i computer del connettore devono essere aggiunti a un dominio che possa delegare gli utenti verso l'applicazione. La delega vincolata Kerberos supporta le funzionalità tra foreste. Tuttavia, per le aziende che hanno ambienti a più foreste distinti senza una relazione di trust tra di essi, è possibile usare un solo connettore per tutte le foreste. 

In tal caso, è possibile distribuire connettori specifici per ogni foresta e impostarli per la gestione delle applicazioni pubblicate per gestire solo gli utenti della foresta specifica. Ogni gruppo di connettori rappresenta una foresta diversa. Mentre il tenant e la maggior parte dell'esperienza sono unificati per tutte le foreste, è possibile assegnare gli utenti alle applicazioni della relativa foresta usando i gruppi di Azure AD.
 
### <a name="disaster-recovery-sites"></a>Siti di ripristino di emergenza

Sono disponibili due diversi approcci ai siti di ripristino di emergenza, a seconda della modalità di implementazione dei siti:

* Se il sito di ripristino di emergenza è compilato in modalità attivo-attivo, per cui è esattamente come il sito principale e ha la stessa rete e le stesse impostazioni di Active Directory, è possibile creare i connettori nel sito di ripristino di emergenza nello stesso gruppo di connettori del sito principale. In questo modo Azure AD può rilevare i failover.
* Se il sito di ripristino di emergenza è separato dal sito principale, è possibile creare un gruppo di connettori diverso nel sito di ripristino di emergenza e 1) avere applicazioni aggiuntive o 2) deviare manualmente l'applicazione esistente verso il gruppo di connettori di ripristino di emergenza, in base alle esigenze.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Gestire più aziende da un singolo tenant

Per implementare un modello in cui un singolo provider di servizi distribuisce e gestisce i servizi correlati ad Azure AD per più aziende, è possibile procedere in diversi modi. I gruppi di connettori permettono all'amministratore di isolare i connettori e le applicazioni in gruppi diversi. Uno dei metodi, adatto alle aziende di piccole dimensioni, consiste nell'avere un singolo tenant di Azure AD mentre le varie aziende hanno reti e nomi di dominio propri. Questo vale anche per scenari di fusione e acquisizione e situazioni in cui un singolo reparto IT gestisce diverse aziende per motivi legali o economici. 

## <a name="sample-configurations"></a>Configurazioni di esempio

Tra gli esempi che è possibile implementare, sono inclusi i gruppi di connettori seguenti.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Configurazione predefinita senza gruppi di connettori

Se non si usano gruppi di connettori, la configurazione ha un aspetto simile al seguente:

![Azure AD senza gruppi di connettori](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Questa configurazione è sufficiente per distribuzioni di piccole dimensioni e test. È adatta anche a organizzazioni con una topologia di rete flat.
 
### <a name="default-configuration-and-an-isolated-network"></a>Configurazione predefinita con una rete isolata

Questa configurazione rappresenta un'evoluzione di quella predefinita. In questa configurazione un'app specifica viene eseguita in una rete isolata, ad esempio una rete virtuale IaaS: 

![Azure AD senza gruppi di connettori](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configurazione consigliata con diversi gruppi specifici e un gruppo predefinito inattivo

La configurazione consigliata per organizzazioni complesse di grandi dimensioni include il gruppo di connettori predefinito come gruppo che non gestisce applicazioni e viene usato per i connettori inattivi o appena installati. Tutte le applicazioni vengono gestite tramite gruppi di connettori personalizzati. Questo rende possibile la complessità degli scenari descritti in precedenza.

Nell'esempio seguente l'azienda ha due data center, A e B, con due connettori che gestiscono ogni sito. In ognuno dei siti vengono eseguite applicazioni diverse. 

![Azure AD senza gruppi di connettori](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Passaggi successivi
* [Abilitare il proxy dell'applicazione](active-directory-application-proxy-enable.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)


