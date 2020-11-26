---
title: Esercitazione per la configurazione dell'identità N8 con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Esercitazione per la configurazione dello strumento di amministrazione di TheAccessHub con Azure Active Directory B2C per gestire la migrazione degli account cliente e l'amministrazione delle richieste di assistenza clienti (CSR).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170923"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione dello strumento di amministrazione di TheAccessHub con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare Azure Active Directory (AD) B2C con [lo strumento di amministrazione TheAccessHub](https://n8id.com/products/theaccesshub-admintool/) da N8 Identity. Questa soluzione risolve la migrazione degli account dei clienti e l'amministrazione delle richieste di assistenza clienti (CSR).  

Questa soluzione è ideale se si dispone di una o più delle seguenti esigenze:

- Si dispone di un sito esistente e si desidera eseguire la migrazione a Azure AD B2C. Tuttavia, la migrazione degli account cliente è in difficoltà, incluse le password

- È necessario uno strumento per la CSR per amministrare gli account Azure AD B2C.

- Si ha la necessità di usare l'amministrazione delegata per la I CSR.

- Si vuole sincronizzare e unire i dati da molti repository in Azure AD B2C.

## <a name="pre-requisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Tenant Azure ad B2C](./tutorial-create-tenant.md). Il tenant deve essere collegato alla sottoscrizione di Azure.

- Ambiente dello strumento di amministrazione di TheAccessHub: contattare l' [identità N8](https://n8id.com/contact/) per eseguire il provisioning di un nuovo ambiente.

- Opzionale Informazioni sulla connessione e le credenziali per i database o i protocolli LDAP (Lightweight Directory Access Protocol) di cui si desidera eseguire la migrazione dei dati del cliente.

- Opzionale Configurato Azure AD B2C ambiente per l'uso di [criteri personalizzati](./custom-policy-get-started.md), se si vuole integrare lo strumento di amministrazione di TheAccessHub nel flusso dei criteri di iscrizione.

## <a name="scenario-description"></a>Descrizione dello scenario

Lo strumento di amministrazione TheAccessHub viene eseguito in modo analogo a qualsiasi altra applicazione in Azure. Può essere eseguito nella sottoscrizione di Azure dell'identità N8 o nella sottoscrizione del cliente. Il diagramma dell'architettura seguente illustra l'implementazione di.

![Immagine che mostra il diagramma dell'architettura di n8identity](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. Gli utenti selezionano l'iscrizione per creare un nuovo account e immettere le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama lo strumento di amministrazione TheAccessHub e passa gli attributi utente
| 3. | Lo strumento di amministrazione di TheAccessHub controlla le informazioni correnti dell'utente nel database esistente.  
| 4. | Il record utente viene sincronizzato dal database allo strumento di amministrazione TheAccessHub.
| 5. | Lo strumento di amministrazione di TheAccessHub condivide i dati con l'amministratore CSR/helpdesk delegato.
| 6. | Lo strumento di amministrazione di TheAccessHub sincronizza i record utente con Azure AD B2C.
| 7. |In base alla risposta di esito positivo o negativo dello strumento di amministrazione di TheAccessHub, Azure AD B2C invia all'utente un messaggio di benvenuto personalizzato.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Creare un amministratore globale nel tenant di Azure AD B2C

Lo strumento di amministrazione di TheAccessHub richiede le autorizzazioni per agire per conto di un amministratore globale per leggere le informazioni sull'utente e apportare modifiche nel tenant del Azure AD B2C. Le modifiche apportate agli amministratori regolari hanno vinto; t influisca sulla capacità dello strumento di amministrazione di TheAccessHub di interagire con il tenant.

Per creare un amministratore globale, attenersi alla procedura seguente:

1. Nella portale di Azure accedere al tenant di Azure AD B2C come amministratore. Passa a **Azure Active Directory**  >  **utenti**
2. Seleziona **nuovo utente**
3. Scegliere **Crea utente** per creare un utente directory normale e non un cliente
4. Completare il modulo informazioni di identità

   a. Immettere il nome utente, ad esempio ' theaccesshub '

   b. Immettere il nome, ad esempio "account del servizio TheAccessHub"

5. Selezionare **Mostra password** e copiare la password iniziale per un uso successivo
6. Assegnare il ruolo di amministratore globale

   a. Selezionare l' **utente** dei ruoli correnti dell'utente per modificarlo

   b. Controllare il record per l'amministratore globale

   c. **Seleziona**  >  **Crea**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Connettere lo strumento di amministrazione di TheAccessHub al tenant di Azure AD B2C

Lo strumento di amministrazione di TheAccessHub USA API Graph Microsoft per leggere e apportare modifiche alla directory. Funge da amministratore globale nel tenant. Sono necessarie autorizzazioni aggiuntive per lo strumento di amministrazione di TheAccessHub, che è possibile concedere dall'interno dello strumento.

Per autorizzare lo strumento di amministrazione di TheAccessHub ad accedere alla directory, seguire questa procedura:

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passa a **System Admin**  >  **Azure ad B2C config**

3. Selezionare **autorizza connessione**

4. Nella nuova finestra accedere con l'account amministratore globale. È possibile che venga richiesto di reimpostare la password se si esegue l'accesso per la prima volta con il nuovo account del servizio.

5. Seguire le istruzioni e selezionare **Accetto** per concedere a TheAccessHub Admin Tool le autorizzazioni richieste.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Configurare un nuovo utente CSR usando l'identità aziendale

Creare un utente CSR/helpdesk che accede allo strumento di amministrazione di TheAccessHub usando le credenziali Azure Active Directory aziendali esistenti.

Per configurare l'utente CSR/helpdesk con Single Sign-on (SSO), è consigliabile seguire questa procedura:

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity.

2. Passare a **Gestione strumenti**  >  **Gestisci colleghi**

3. Selezionare **Aggiungi collega**

4. Seleziona un **collega digitare amministratore di Azure**

5. Immettere le informazioni sul profilo del collega

   a. La scelta di un'organizzazione principale controllerà chi dispone dell'autorizzazione per la gestione di questo utente.

   b. Per ID di accesso/Azure AD nome utente specificare il nome dell'entità utente dall'account Azure Active Directory dell'utente.

   c. Nella scheda ruoli TheAccessHub selezionare il supporto tecnico per il ruolo gestito. Consente all'utente di accedere alla visualizzazione Gestisci colleghi. L'utente dovrà comunque essere inserito in un gruppo o essere un proprietario dell'organizzazione per agire sui clienti.

6. Selezionare **Submit** (Invia).

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Configurare un nuovo utente CSR usando una nuova identità

Creare un utente CSR/helpdesk per accedere allo strumento di amministrazione di TheAccessHub con una nuova credenziale locale univoca per lo strumento di amministrazione di TheAccessHub. Questa operazione verrà utilizzata principalmente dalle organizzazioni che non utilizzano un Azure AD per la propria azienda.

Per [configurare un utente CSR/helpdesk](https://youtu.be/iOpOI2OpnLI) senza SSO, attenersi alla procedura seguente:

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passare a **Gestione strumenti**  >  **Gestisci colleghi**

3. Selezionare **Aggiungi collega**

4. Selezione **tipo di collega amministratore locale**

5. Immettere le informazioni sul profilo del collega

   a. La scelta di un'organizzazione principale controllerà chi dispone dell'autorizzazione per la gestione di questo utente.

   b. Nella scheda **ruoli TheAccessHub** selezionare il **supporto tecnico** per il ruolo gestito. Consente all'utente di accedere alla visualizzazione Gestisci colleghi. L'utente dovrà comunque essere inserito in un gruppo o essere un proprietario dell'organizzazione per agire sui clienti.

6. Copiare gli attributi **ID di accesso/posta elettronica** e **password una volta** . Fornire il nuovo utente. Queste credenziali verranno usate per accedere allo strumento di amministrazione TheAccessHub. All'utente verrà richiesto di immettere una nuova password al primo accesso.

7. Selezionare **Invia**

## <a name="configure-partitioned-csr-administration"></a>Configurare l'amministrazione CSR partizionata

Le autorizzazioni per gestire gli utenti Customer e CSR/helpdesk nello strumento di amministrazione di TheAccessHub sono gestite con l'uso di una gerarchia dell'organizzazione. Tutti i colleghi e i clienti hanno un'organizzazione principale in cui risiedono. I colleghi o i gruppi di colleghi specifici possono essere assegnati come proprietari di organizzazioni.  I proprietari dell'organizzazione possono gestire (apportare modifiche a) i colleghi e i clienti di organizzazioni o sottoorganizzazioni di loro proprietà. Per consentire a più colleghi di gestire un set di utenti, è possibile creare un gruppo con molti membri. Il gruppo può quindi essere assegnato come proprietario dell'organizzazione e tutti i membri del gruppo possono gestire i colleghi e i clienti dell'organizzazione.

### <a name="create-a-new-group"></a>Creare un nuovo gruppo

1. Accedere a TheAccessHub Admin Tool usando le **credenziali** fornite da N8 Identity

2. Passa a **organizzazione > Gestisci gruppi**

3. Selezionare > **Aggiungi gruppo**

4. Immettere il **nome del gruppo**, la **Descrizione del gruppo** e il **proprietario del gruppo**

5. Cercare e selezionare le caselle sui colleghi che si desidera siano membri del gruppo, quindi selezionare >**Aggiungi**

6. Nella parte inferiore della pagina è possibile visualizzare tutti i membri del gruppo.

7. Se è possibile rimuovere membri necessari selezionando la **x** alla fine della riga

8. Selezionare **Invia**

### <a name="create-a-new-organization"></a>Creare una nuova organizzazione

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passare a organizzazione > **gestire le organizzazioni**

3. Selezionare > **Aggiungi organizzazione**

4. Fornire un **nome di organizzazione**, un **proprietario dell'organizzazione** e un' **organizzazione padre**.

    a. Il nome dell'organizzazione è idealmente un valore che corrisponde ai dati del cliente. Quando si caricano i dati dei colleghi e dei clienti, se si specifica il nome dell'organizzazione nel carico, il collega può essere inserito automaticamente nell'organizzazione.

    b. Il proprietario rappresenta la persona o il gruppo che gestirà i clienti e i colleghi dell'organizzazione e le eventuali sottoorganizzazioni all'interno di.

    c. L'organizzazione padre indica l'altra organizzazione che è intrinsecamente responsabile di questa organizzazione.

5. Selezionare **Submit** (Invia).

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Modificare la gerarchia tramite la visualizzazione albero

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passa alla **Manager Tools**  >  **visualizzazione albero** degli strumenti di gestione

3. In questa rappresentazione è possibile visualizzare i colleghi e i gruppi che possono gestire le organizzazioni.

4. Le gerarchie possono essere modificate trascinando le organizzazioni sovrastano le organizzazioni di cui si vuole avere un elemento padre.

5. Al termine della modifica della gerarchia, fare clic su **Salva** .

## <a name="customize-welcome-notification"></a>Personalizzare la notifica introduttiva

Quando si usa TheAccessHub per eseguire la migrazione degli utenti da una soluzione di autenticazione precedente in Azure AD B2C, è possibile personalizzare la notifica di benvenuto dell'utente, che viene inviata all'utente da TheAccessHub durante la migrazione. Questo messaggio include in genere il collegamento per il cliente per impostare una nuova password nella directory Azure AD B2C.

Per personalizzare la notifica:

1. Accedere a TheAccessHub usando le credenziali fornite da N8 Identity

2. Passa a **Notifiche amministratore sistema**  >  **Notifications**

3. Selezionare il **modello crea collega**

4. Selezionare **Modifica**.

5. Modificare i campi del messaggio e del modello se necessario. Il campo modello è compatibile con HTML e può inviare notifiche HTML formattate ai messaggi di posta elettronica dei clienti.

6. Al termine, selezionare **Salva** .

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrare i dati da origini dati esterne a Azure AD B2C

Usando lo strumento di amministrazione di TheAccessHub, è possibile importare dati da diversi database, LDAP e file CSV, quindi eseguire il push dei dati nel tenant Azure AD B2C. A tale scopo, è necessario caricare i dati nel tipo di collega Azure AD B2C utente nello strumento di amministrazione TheAccessHub.  Se l'origine dei dati non è Azure, i dati verranno inseriti sia nello strumento di amministrazione di TheAccessHub sia in Azure AD B2C. Se l'origine dei dati esterni non è un file CSV semplice nel computer, configurare un'origine dati prima di eseguire il caricamento dei dati. Nei passaggi seguenti viene descritta la creazione di un'origine dati e il caricamento dei dati.

### <a name="configure-a-new-data-source"></a>Configurare una nuova origine dati

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passa a **System Admin**  >  **origini dati** dell'amministratore di sistema

3. Selezionare **Aggiungi origine dati**

4. Specificare un **nome** e un **tipo** per questa origine dati

5. Compilare i dati del modulo, a seconda del tipo di origine dati:

   **Per i database**

   a. **Tipo** : database

   b. **Tipo di database** : selezionare un database da uno dei tipi di database supportati.

   c. **URL connessione** : immettere una stringa di connessione JDBC ben formattata. Ad esempio: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Nome utente** : immettere il nome utente per l'accesso al database

   e. **Password** : immettere la password per l'accesso al database

   f. **Query** : immettere la query SQL per estrarre i dettagli del cliente. Ad esempio: ``SELECT * FROM mytable;``

   g. Selezionare **Test connessione**. verrà visualizzato un esempio dei dati per assicurarsi che la connessione funzioni.

   **Per LDAPs**

   a. **Tipo** : LDAP

   b. **Host** : immettere il nome host o l'IP per il computer in cui è in esecuzione il server LDAP. Ad esempio: ``mysite.com``

   c. **Porta** : immettere il numero di porta in cui il server LDAP è in ascolto.

   d. **SSL** : selezionare la casella se lo strumento di amministrazione di TheAccessHub deve comunicare con LDAP in modo sicuro tramite SSL. Si consiglia di usare SSL.

   e. **DN di accesso** : immettere il nome distinto dell'account utente per l'accesso e la ricerca LDAP

   f. **Password** : immettere la password per l'utente

   g. **DN di base** : immettere il nome distinto nella parte superiore della gerarchia in cui si desidera eseguire la ricerca

   h. **Filter (filtro** ): immettere la stringa di filtro LDAP, che otterrà i record del cliente

   i. **Attributi** : immettere un elenco delimitato da virgole di attributi dai record dei clienti da passare allo strumento di amministrazione di TheAccessHub

   j. Selezionare la **connessione di test**. verrà visualizzato un esempio dei dati per assicurarsi che la connessione funzioni.

   **Per OneDrive**

   a. **Tipo** : OneDrive for business

   b. Selezionare **autorizza connessione**

   c. Una nuova finestra richiederà di accedere a **OneDrive** e di accedere con un utente con accesso in lettura all'account OneDrive. Lo strumento di amministrazione di TheAccessHub consente all'utente di leggere i file di caricamento CSV.

   d. Seguire le istruzioni e selezionare **Accetto** per concedere a TheAccessHub Admin Tool le autorizzazioni richieste.

6. Al termine, selezionare **Salva** .  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Sincronizzare i dati dall'origine dati in Azure AD B2C

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passa alla **System Admin**  >  **sincronizzazione dei dati** dell'amministratore di sistema

3. Seleziona **nuovo carico**

4. Selezionare il **tipo di collega** Azure ad B2C utente

5. Selezionare **origine**, nella finestra di dialogo popup, selezionare l'origine dati. Se è stata creata un'origine dati OneDrive, selezionare anche il file.

6. Se non si vuole creare nuovi account cliente con questo carico, modificare il primo criterio: se il **collega non è stato trovato in TheAccessHub** , **non eseguire alcuna operazione**

7. Se non si vuole aggiornare gli account cliente esistenti con questo carico, modificare il secondo criterio **se i dati di origine e di TheAccessHub non corrispondono, quindi** **non eseguire alcuna operazione**

8. Selezionare **Avanti**

9. Nella **configurazione di mapping di ricerca** è possibile identificare come correlare i record di caricamento con i clienti già caricati nello strumento di amministrazione di TheAccessHub. Scegliere uno o più attributi di identificazione nell'origine. Trovare la corrispondenza con gli attributi con un attributo nello strumento di amministrazione TheAccessHub che contenga gli stessi valori. Se viene trovata una corrispondenza, il record esistente verrà sottoposto a override; in caso contrario, verrà creato un nuovo cliente. È possibile sequenziare un certo numero di controlli. Ad esempio, è possibile controllare prima di tutto la posta elettronica, quindi il nome e il cognome.

10. Nel menu sul lato sinistro selezionare **mapping dei dati**.

11. Nella configurazione di Data-Mapping assegnare gli attributi dello strumento di amministrazione di TheAccessHub da popolare dagli attributi di origine. Non è necessario eseguire il mapping di tutti gli attributi. Gli attributi senza mapping rimarranno invariati per i clienti esistenti.

12. Se si esegue il mapping all'attributo org_name con un valore che corrisponde al nome di un'organizzazione esistente, i nuovi clienti creati verranno posizionati nell'organizzazione.

13. Selezionare **Avanti**

14. Se il carico deve ripetersi, è possibile specificare una pianificazione giornaliera, settimanale o mensile. In caso **contrario, Mantieni** il valore predefinito.

15. Selezionare **Invia**

16. Se è stata selezionata la **pianificazione ora** , un nuovo record verrà aggiunto immediatamente alla schermata sincronizzazioni dati. Dopo che la fase di convalida ha raggiunto il 100%, selezionare il **nuovo record** per visualizzare il risultato previsto per il carico. Per i caricamenti pianificati, questi record verranno visualizzati solo dopo l'orario pianificato.

17. Se non sono presenti errori, selezionare **Esegui per eseguire** il commit delle modifiche. In caso contrario, selezionare **Rimuovi** dal menu **altro** per rimuovere il carico. È quindi possibile correggere i dati di origine o i mapping di caricamento e riprovare. Se invece il numero di errori è ridotto, è possibile aggiornare manualmente i record e selezionare **Aggiorna** in ogni record per apportare correzioni. Infine, è possibile continuare con eventuali errori e risolverli in seguito come **interventi di supporto** nello strumento di amministrazione TheAccessHub.

18. Quando il record di **sincronizzazione dei dati** diventa il 100% per la fase di caricamento, saranno avviate tutte le modifiche risultanti dal carico. I clienti devono iniziare a comparire o ricevere modifiche in Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Sincronizzare i dati del cliente Azure AD B2C 

Come operazione monouso o continuo, lo strumento di amministrazione di TheAccessHub può sincronizzare tutte le informazioni del cliente da Azure AD B2C nello strumento di amministrazione di TheAccessHub. Ciò garantisce che gli amministratori di CSR/helpdesk visualizzino informazioni aggiornate sui clienti.

Per sincronizzare i dati da Azure AD B2C nello strumento di amministrazione di TheAccessHub:

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passa alla **System Admin**  >  **sincronizzazione dei dati** dell'amministratore di sistema

3. Seleziona **nuovo carico**

4. Selezionare il **tipo di collega** Azure ad B2C utente

5. Per il passaggio **Opzioni** , lasciare le impostazioni predefinite.

6. Selezionare **Avanti**

7. Per il **mapping dei dati &** passaggio di ricerca, lasciare le impostazioni predefinite. Eccetto se si esegue il mapping all'attributo **ORG_NAME** con un valore che corrisponde al nome di un'organizzazione esistente, i nuovi clienti creati verranno posizionati nell'organizzazione.

8. Selezionare **Avanti**

9. Se il carico deve ripetersi, è possibile specificare una pianificazione giornaliera, settimanale o mensile. In caso contrario, Mantieni il valore predefinito: **Now**. È consigliabile eseguire periodicamente la sincronizzazione da Azure AD B2C.

10. Selezionare **Invia**

11. Se è stata selezionata la pianificazione **ora** , un nuovo record verrà aggiunto immediatamente alla schermata sincronizzazioni dati. Dopo che la fase di convalida ha raggiunto il 100%, selezionare il nuovo record per visualizzare il risultato previsto per il carico. Per i caricamenti pianificati, questi record verranno visualizzati solo dopo l'orario pianificato.

12. Se non sono presenti errori, selezionare **Esegui per eseguire** il commit delle modifiche. In caso contrario, selezionare **Rimuovi** dal menu altro per rimuovere il carico. È quindi possibile correggere i dati di origine o i mapping di caricamento e riprovare. Se invece il numero di errori è ridotto, è possibile aggiornare manualmente i record e selezionare **Aggiorna** in ogni record per apportare correzioni. Infine, è possibile continuare con eventuali errori e risolverli in seguito come interventi di supporto nello strumento di amministrazione TheAccessHub.

13. Quando il record di **sincronizzazione dei dati** diventa il 100% per la fase di caricamento, saranno avviate tutte le modifiche risultanti dal carico.

## <a name="configure-azure-ad-b2c-policies"></a>Configurare i criteri di Azure AD B2C

Occasionalmente la sincronizzazione dello strumento di amministrazione di TheAccessHub è limitata alla capacità di Mantenete aggiornato lo stato con Azure AD B2C. Possiamo sfruttare l'API dello strumento di amministrazione di TheAccessHub e i criteri di Azure AD B2C per informare lo strumento di amministrazione di TheAccessHub delle modifiche appena si verificano. Questa soluzione richiede una conoscenza tecnica di [Azure ad B2C criteri personalizzati](./custom-policy-get-started.md). Nella sezione successiva verranno illustrati i passaggi dei criteri di esempio e un certificato sicuro per notificare allo strumento di amministrazione TheAccessHub i nuovi account nei criteri personalizzati Sign-Up.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Creare credenziali sicure per richiamare l'API dello strumento di amministrazione di TheAccessHub

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passare a strumenti di amministrazione amministratore di **sistema**  >  **Admin Tools**  >  **sicurezza API**

3. Selezionare **genera**

4. Copiare la **password del certificato**

5. Selezionare **download** per ottenere il certificato client.

6. Seguire questa [esercitazione](./secure-rest-api.md#https-client-certificate-authentication ) per aggiungere il certificato client in Azure ad B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Recuperare gli esempi di criteri personalizzati

1. Accedere a TheAccessHub Admin Tool usando le credenziali fornite da N8 Identity

2. Passare a strumenti di amministrazione amministratore di **sistema**  >  **Admin Tools**  >  **criteri di Azure B2C**

3. Fornire il dominio del tenant Azure AD B2C e i due ID Framework dell'esperienza di identità dalla configurazione del Framework dell'esperienza di identità

4. Selezionare **Salva**

5. Selezionare **download** per ottenere un file zip con criteri di base che aggiungono i clienti allo strumento di amministrazione di TheAccessHub quando i clienti si iscrivono.

6. Seguire questa [esercitazione](./custom-policy-get-started.md) per iniziare a progettare criteri personalizzati in Azure ad B2C.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)