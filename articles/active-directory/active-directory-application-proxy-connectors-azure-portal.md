---
title: Pubblicare applicazioni in reti e posizioni separate tramite gruppi di connettori nel proxy di applicazione di Azure AD | Microsoft Docs
description: Illustra come creare e gestire i gruppi di connettori nel proxy di applicazione di Azure AD.
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 7c409e5419a2bd393cb5c7ec265e44310f7e9027
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori

Il proxy di applicazione di Azure AD viene usato per un numero crescente di scenari e applicazioni dei clienti. Per questo motivo, il proxy di applicazione è stato reso ancora più flessibile grazie all'abilitazione di altre topologie. È possibile creare gruppi di connettori proxy di applicazione in modo da poter assegnare connettori specifici per la gestione di determinate applicazioni. Questa funzionalità offre un controllo maggiore e più metodi per ottimizzare la distribuzione dei proxy di applicazione. 

Ogni connettore proxy di applicazione viene assegnato a un gruppo di connettori. Tutti i connettori che appartengono allo stesso gruppo di connettori costituiscono un'unità separata per la disponibilità elevata e il bilanciamento del carico. Tutti i connettori appartengono a un gruppo di connettori. Se non si creano i gruppi, tutti i connettori vengono inclusi in un gruppo predefinito. L'amministratore può creare nuovi gruppi e assegnare connettori ai gruppi usando il portale di Azure. 

Tutte le applicazioni sono assegnate a un gruppo di connettori. Se non si creano i gruppi, tutte le applicazioni vengono assegnate a un gruppo predefinito. Se però i connettori vengono organizzati in gruppi, è possibile impostare ogni applicazione per l'uso di un gruppo di connettori specifico. In questo caso, vengono usati su richiesta solo i connettori inclusi nel gruppo specifico. Questa funzionalità è utile se le applicazioni sono ospitate in posizioni diverse. È possibile creare gruppi di connettori in base alla posizione, in modo che le applicazioni vengano sempre gestite dai connettori che si trovano fisicamente più vicini.

>[!TIP] 
>Nel caso di una distribuzione di proxy di applicazione di grandi dimensioni, non assegnare alcuna applicazione al gruppo di connettori predefinito. In questo caso, i nuovi connettori non riceveranno traffico live finché non vengono assegnati a un gruppo di connettori attivi. Questa configurazione permette anche di applicare ai connettori una modalità inattiva spostandoli di nuovo nel gruppo predefinito, in modo da eseguire operazioni di manutenzione senza alcun impatto sugli utenti.

## <a name="prerequisites"></a>Prerequisiti
Per raggruppare i connettori è necessario assicurarsi di avere [installato più connettori](active-directory-application-proxy-enable.md). Quando si installa un nuovo connettore, questo si aggiunge automaticamente al gruppo di connettori **predefinito** .

## <a name="create-connector-groups"></a>Creare gruppi di connettori
Usare questi passaggi per creare il numero di gruppi di connettori desiderato. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Proxy dell'applicazione**.
2. Selezionare **Nuovo gruppo di connettori**. Viene visualizzato il pannello New Connector Group (Nuovo gruppo di connettori).

   ![Selezionare il nuovo gruppo di connettori](./media/active-directory-application-proxy-connectors-azure-portal/new-group.png)

3. Assegnare un nome al nuovo gruppo di connettori, quindi usare il menu a discesa per selezionare i connettori appartenenti a questo gruppo.
4. Selezionare **Salva**.

## <a name="assign-applications-to-your-connector-groups"></a>Assegnare applicazioni ai gruppi di connettori
Usare questi passaggi per ogni applicazione pubblicata con il proxy di applicazione. È possibile assegnare un'applicazione a un gruppo di connettori quando l'applicazione viene pubblicata per la prima volta oppure è possibile usare questi passaggi per modificare l'assegnazione ogni volta che è necessario.   

1. Nel dashboard di gestione per la directory selezionare **Applicazioni aziendali** > **All applications** (Tutte le applicazioni) > l'applicazione che si vuole assegnare a un gruppo di connettori > **Proxy dell'applicazione**.
2. Usare il menu a discesa **Gruppo di connettori** per selezionare il gruppo che dovrà essere usato dall'applicazione.
3. Fare clic su **Salva** per salvare la modifica.

## <a name="use-cases-for-connector-groups"></a>Casi d'uso per gruppi di connettori 

I gruppi di connettori sono utili in varie situazioni, ad esempio:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Siti con più data center interconnessi

Molte organizzazioni hanno diversi data center interconnessi. In questo caso è preferibile mantenere la maggior quantità di traffico possibile all'interno del data center, perché i collegamenti tra data center sono lenti e dispendiosi. È possibile distribuire connettori in ogni data center per rendere disponibili solo le applicazioni che risiedono all'interno del data center. Questo approccio riduce al minimo i collegamenti tra data center e offre un'esperienza completamente trasparente agli utenti.

### <a name="applications-installed-on-isolated-networks"></a>Applicazioni installate in reti isolate

È possibile ospitare le applicazioni in reti che non fanno parte della rete aziendale principale. È possibile usare gruppi di connettori per installare connettori dedicati in reti isolate, in modo da isolare anche le applicazioni per la rete. Questo accade in genere quando un fornitore di terze parti gestisce un'applicazione specifica per l'organizzazione. 

I gruppi di connettori permettono di installare connettori dedicati per le reti che pubblicano solo applicazioni specifiche, rendendo più semplice e sicuro l'outsourcing della gestione delle applicazioni a fornitori di terze parti.

### <a name="applications-installed-on-iaas"></a>Applicazioni installate in IaaS 

Per le applicazioni installate in IaaS per l'accesso al cloud, i gruppi di connettori offrono un servizio comune per proteggere l'accesso a tutte le app. I gruppi di connettori non creano dipendenza aggiuntiva dalla rete aziendale o non frammentano l'esperienza delle app. I connettori possono essere installati in ogni data center nel cloud e gestire solo le applicazioni che si trovano nella rete. È possibile installare più connettori per ottenere una disponibilità elevata.

Considerare come esempio un'organizzazione con diverse macchine virtuali connesse alla rete virtuale IaaS ospitata. Per permettere ai dipendenti di usare le applicazioni, tali reti private sono connesse alla rete aziendale tramite VPN da sito a sito. Questa soluzione offre un'esperienza ottimale ai dipendenti a livello locale. Può tuttavia non essere ideale per i dipendenti che lavorano in remoto, poiché richiede un'infrastruttura locale aggiuntiva per instradare l'accesso, come illustra il diagramma seguente:

![Rete IaaS di Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Con i gruppi di connettori del proxy di applicazione di Azure AD, è possibile abilitare un servizio comune per proteggere l'accesso a tutte le applicazioni senza creare una dipendenza aggiuntiva nella rete aziendale:

![Più fornitori cloud IaaS per Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Scenario a più foreste: gruppi di connettori diversi per ogni foresta

La maggior parte dei clienti che hanno distribuito il proxy di applicazione usa le funzionalità Single Sign-On (SSO) eseguendo la delega vincolata Kerberos (KCD). A questo scopo, i computer del connettore devono essere aggiunti a un dominio in grado di delegare gli utenti verso l'applicazione. La delega vincolata Kerberos supporta le funzionalità tra foreste. Tuttavia, per le aziende che hanno ambienti a più foreste distinti senza una relazione di trust tra di essi, è possibile usare un solo connettore per tutte le foreste. 

In tal caso, è possibile distribuire connettori specifici per ogni foresta e impostarli per la gestione delle applicazioni pubblicate per gestire solo gli utenti della foresta specifica. Ogni gruppo di connettori rappresenta una foresta diversa. Mentre il tenant e buona parte dell'esperienza sono unificati per tutte le foreste, è possibile assegnare utenti alle applicazioni della rispettiva foresta tramite i gruppi di Azure AD.
 
### <a name="disaster-recovery-sites"></a>Siti di ripristino di emergenza

Sono disponibili due diversi approcci ai siti di ripristino di emergenza, a seconda della modalità di implementazione dei siti:

* Se il sito di ripristino di emergenza è compilato in modalità attivo-attivo, per cui è esattamente come il sito principale e ha la stessa rete e le stesse impostazioni di Active Directory, è possibile creare i connettori nel sito di ripristino di emergenza nello stesso gruppo di connettori del sito principale. In questo modo Azure AD può rilevare i failover.
* Se il sito di ripristino di emergenza è separato dal sito principale, è possibile creare un gruppo di connettori diverso nel sito di ripristino di emergenza e 1) predisporre applicazioni di backup o 2) deviare manualmente l'applicazione esistente verso il gruppo di connettori di ripristino di emergenza, in base alle esigenze.
 
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

* [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)
* [Abilitare l'accesso Single Sign-On](application-proxy-sso-overview.md)


