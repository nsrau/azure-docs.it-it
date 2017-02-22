---
title: Abilitare l&quot;accesso remoto a SharePoint con il proxy applicazione Azure AD | Documentazione Microsoft
description: Tratta i fondamenti dei connettori del proxy applicazione Azure AD.
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 8436238546515b66b58b31673d54586e4a20f50f
ms.openlocfilehash: 86f10c04368d1c382939b418c6909ca807b3bf5a


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-app-proxy"></a>Abilitare l'accesso remoto a SharePoint con il proxy applicazione Azure AD

Questo articolo illustra come integrare il server SharePoint locale nel proxy applicazione Azure AD.

> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 

##<a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che SharePoint 2013 o versione successiva sia già installato e in esecuzione nell'ambiente. Tenere in considerazione anche i seguenti prerequisiti:

* SharePoint include il supporto nativo di Kerberos. Di conseguenza, gli utenti che accedono ai siti interni in modalità remota tramite il proxy applicazione Azure AD possono presumere di avere una perfetta esperienza dell'accesso Single Sign-On.

* È necessario apportare alcune modifiche di configurazione al server SharePoint. Si consiglia di usare un ambiente di gestione temporanea. In questo modo è possibile eseguire prima gli aggiornamenti al server di gestione temporanea e quindi facilitare un ciclo di test prima del passaggio in produzione.

* Si presuppone che sia già stato configurato SSL per SharePoint, in quanto è richiesto SSL sull'URL pubblicato. È necessario che SSL sia abilitato nel sito interno per garantire che i collegamenti siano inviati e mappati correttamente. Se SSL non è stato configurato, vedere [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) (Configurare SSL per SharePoint 2013) che riporta le istruzioni per la configurazione di SSL. Inoltre assicurarsi che il computer di connessione consideri attendibile il certificato che si emette. Questo non deve essere necessariamente un certificato rilasciato pubblicamente.

##<a name="steps-to-set-up-sharepoint"></a>Passaggi per la configurazione di SharePoint

Per abilitare l'accesso remoto a SharePoint con il proxy applicazione Azure AD procedere come segue:

**Parte 1: Configurare l'accesso Single Sign-On (SSO)**

  * Passaggio A. Assicurarsi che il server SharePoint sia in esecuzione come account di servizio.
  * Passaggio B. Configurare SharePoint per Kerberos.
  * Passaggio C. Impostare un nome di entità servizio (SPN) per l'account assegnato a SharePoint.
  * Passaggio D. Verificare che il connettore sia impostato come delegato attendibile a SharePoint.

**Parte 2: Abilitare l'accesso remoto sicuro**

 * Pubblicare la farm di SharePoint sul proxy applicazione Azure AD.

**Parte 3: Assicurarsi che SharePoint sia al corrente dell'URL esterno**

 * Impostare mapping di accesso alternativo in SharePoint.

### <a name="part-1-set-up-single-sign-on-sso-to-sharepoint"></a>Parte 1: Configurare l'accesso Single Sign-On (SSO) a SharePoint

I nostri clienti vogliono la migliore esperienza di accesso SSO alle applicazioni back-end, il server SharePoint in questo caso. In tale scenario comune di Azure AD l'utente eseguirà l'autenticazione una sola volta, in quanto l'autenticazione non verrà richiesta nuovamente.

Per le applicazioni locali che richiedono o usano l'autenticazione di Windows è possibile ottenerla usando il protocollo di autenticazione Kerberos e una funzionalità denominata delega vincolata Kerberos (KCD). La delega vincolata Kerberos, quando è configurata, consente al connettore del proxy applicazione di ottenere un ticket o token di Windows per un determinato utente, anche se l'utente non ha eseguito l'accesso direttamente in Windows. Per altre informazioni sulla delega vincolata Kerberos, vedere [Panoramica della delega vincolata Kerberos](https://technet.microsoft.com/en-us/library/jj553400.aspx).

Per eseguire la configurazione per un server SharePoint procedere come segue.

**Passaggio A: Verificare che SharePoint sia in esecuzione in un account di servizio, non in un sistema locale, un servizio locale o un servizio di rete**

La prima cosa che è necessario fare è verificare che SharePoint sia in esecuzione con un account di servizio definito. È necessario eseguire questa operazione in modo da poter associare i nomi di entità servizio (SPN) a un account valido. I nomi SPN sono usati dal protocollo Kerberos per identificare i diversi servizi. E saranno necessari in seguito per configurare la delega vincolata Kerberos.

Per garantire che i siti siano in esecuzione in un account di servizio definito, procedere come segue:

1. Aprire il sito **Amministrazione centrale SharePoint 2013**.
2. Passare a **Sicurezza** e scegliere **Configura account di servizio**.
3. Selezionare **Pool di applicazioni Web - SharePoint - 80**. Le opzioni potrebbero essere leggermente diverse in base al nome del pool Web o al fatto che usi SSL per impostazione predefinita.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. Se l'opzione attiva per **Selezionare un account per il componente** è **Servizio locale** o **Servizio di rete** sarà necessario creare un account. In caso contrario l'operazione è terminata e si può procedere con il passaggio successivo. 
5. Scegliere **Registra nuovo account gestito**. Dopo aver creato l'account, prima di poterlo usare bisogna impostare il **Pool di applicazioni Web**.

> [!NOTE]
È necessario avere un account di AD creato in precedenza per il servizio. Si consiglia di consentire la modifica automatica della password. Per altre informazioni sull'insieme completo di passaggi e la risoluzione dei problema, vedere [Configure automatic password change in SharePoint 2013](https://technet.microsoft.com/EN-US/library/ff724280.aspx) (Configurare la modifica automatica della password in SharePoint 2013). 

**Passaggio B. Configurare SharePoint per Kerberos**

Si usa la delega vincolata Kerberos per eseguire l'accesso Single Sign-On (SSO) sul server SharePoint, e questo funziona solo con Kerberos. 

Per configurare il sito di SharePoint per l'autenticazione Kerberos:

1. Aprire il sito **Amministrazione centrale SharePoint 2013**.
2. Passare a **Gestione applicazioni**, scegliere **Gestisci applicazioni Web** e selezionare il sito di SharePoint. In questo esempio si tratta di **SharePoint - 80**.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)
  
3. Fare clic su **Provider di autenticazione** sulla barra degli strumenti.
4. Nella casella **Provider di autenticazione** fare clic su **Area predefinita** per visualizzare le impostazioni.
5. Nella casella **Modifica autenticazione** scorrere verso il basso fino a visualizzare **Tipi di autenticazione delle attestazioni** e assicurarsi che entrambe le opzioni **Abilita autenticazione di Windows** e **Autenticazione di Windows integrata** siano selezionate. 
6. Nella casella di riepilogo a discesa assicurarsi che l'opzione **Negozia (Kerberos)** sia selezionata.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. In fondo alla casella **Modifica autenticazione** fare clic su **Salva**.

**Passaggio C: Impostare un nome SPN per l'account del servizio SharePoint**

Prima di configurare la delega vincolata Kerberos è necessario identificare il servizio SharePoint in esecuzione come account del servizio configurato in precedenza. Lo si fa impostando un nome di entità servizio (SPN). Per altre informazioni vedere [Service Principal Names](https://technet.microsoft.com/en-us/library/cc961723.aspx) (Nomi di entità servizio).

Il formato del nome SPN è:

```
<service class>/<host>:<port>
```

La _classe di servizio_ è un nome univoco per il servizio. Per SharePoint si usa HTTP.

L'_host_ è il nome di dominio completo o il nome Netbios dell'host in cui il servizio è in esecuzione. Nel caso di un sito di SharePoint potrebbe essere necessario che si tratti dell'URL del sito, a seconda della versione di IIS in uso.

La _porta_ è opzionale. Se il nome di dominio completo del server SharePoint è:

```
sharepoint.demo.o365identity.us
```

Il nome SPN sarà: 

```
HTTP/ sharepoint.demo.o365identity.us demo
```

Oltre a questo potrebbe essere necessario impostare i nomi SPN per siti specifici sul server. Per altre informazioni, vedere [Configurare l'autenticazione Kerberos](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx). Leggere con attenzione la sezione "Creare nomi delle entità servizio per applicazioni Web mediante l'autenticazione Kerberos". 

Il modo più semplice per eseguire questa operazione è seguire i formati dei nomi SPN che potrebbero essere già presenti per il sito. Copiare i nomi SPN per la registrazione con l'account del servizio. A tale scopo, seguire questa procedura:

1. Passare al sito con il nome SPN da un altro computer. 
 Quando si esegue questa operazione, il gruppo di ticket Kerberos pertinenti viene memorizzato nella cache del computer. Questi ticket contengono il nome SPN del sito di destinazione a cui si è passati. È possibile estrarre il nome SPN per il sito usando uno strumento denominato [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html).
 
2. In una finestra di comando in esecuzione nello stesso contesto dell'utente che ha eseguito l'accesso al sito nel browser eseguire il comando seguente: 
```
Klist
```
3. Verrà restituito il set di nomi SPN del servizio di destinazione. In questo esempio il valore evidenziato è il nome SPN richiesto:

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)
  
4. Ora che si dispone del nome SPN, è necessario assicurarsi che sia configurato correttamente per l'account del servizio impostato in precedenza per l'applicazione Web. Completare i passaggi della sezione successiva.

**Impostare il nome SPN**

Per impostare il nome SPN, eseguire il comando seguente dal prompt dei comandi come amministratore del dominio.

```
setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
```

Questo comando imposta il nome SPN per l'account del servizio SharePoint in esecuzione come _demo\sp_svc_.

Ricordare di sostituire _http/sharepoint.demo.o365identity.us_ con il nome SPN per il server e _demo\sp_svc_ con l'account del servizio nell'ambiente in uso. Il comando setspn cercherà il nome SPN prima di aggiungerlo. In questo caso si potrebbe ottenere un errore di **valore SPN duplicato**. Se viene visualizzato questo errore, assicurarsi che il valore sia associato con l'account del servizio.

È possibile verificare che il nome SPN sia stato aggiunto tramite il comando Setspn con l'opzione -l. Per altre informazioni sullo strumento Setspn, vedere [Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx).

**Passaggio D: Assicurarsi che i connettori siano attendibili per la delega a SharePoint**

In questo passaggio si configurerà la delega vincolata Kerberos in modo che il servizio proxy applicazione Azure AD sia in grado di delegare le identità utente al servizio SharePoint. Si procede consentendo al connettore del proxy applicazione di recuperare i ticket Kerberos per gli utenti che si sono autenticati in Azure AD. Quindi il server passerà il contesto all'applicazione di destinazione o, in questo caso, a SharePoint. 

Per configurare la delega vincolata Kerberos è necessario ripetere i passaggi seguenti per ogni computer di connessione:

1. Eseguire l'accesso come amministratore a un controller di dominio e quindi aprire un dominio **Utenti e computer di Active Directory**.
2. Trovare il computer in cui è in esecuzione il connettore. In questo esempio è lo stesso server SharePoint.
3. Fare doppio clic sul computer e quindi sulla scheda **Delega**.
4. Assicurarsi che l'impostazione attiva per la delega sia **Computer attendibile per la delega solo ai servizi specificati** quindi selezionare **Usa un qualsiasi protocollo di autenticazione**.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)
  
5. A questo punto è necessario aggiungere il nome SPN creato in precedenza per l'account del servizio. Fare clic sul pulsante **Aggiungi** e quindi su **Utenti o computer** e individuare l'account creato in precedenza.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

 Si vedrà un elenco di nomi SPN fra cui scegliere. È necessario aggiungere quello impostato in precedenza. 
6. Selezionare la voce e fare clic su **OK**. Fare nuovamente clic su **OK** per salvare la modifica.

### <a name="part-2-enable-remote-access-to-sharepoint"></a>Parte 2: Abilitare l'accesso remoto a SharePoint

Ora che SharePoint è stato abilitato per Kerberos ed è stata configurata la delega vincolata Kerberos, è possibile configurare l'accesso Single Sign-On (SSO) a SharePoint. Quindi, dal connettore, è possibile pubblicare SharePoint per l'accesso remoto tramite il proxy applicazione Azure AD.

**Pubblicare SharePoint con il proxy applicazione Azure AD**

Per eseguire la procedura riportata di seguito è necessario essere un membro del ruolo Amministratore globale nell'account di Azure Active Directory dell'organizzazione.

1. Eseguire l'accesso al portale di gestione di Azure https://manage.windowsazure.com e individuare il tenant Azure AD.
2. Fare clic su **Applicazioni** e quindi su **Aggiungi**.
3. Selezionare **Pubblica un'applicazione che sarà accessibile dall'esterno della rete**. Se questa opzione non è presente, assicurarsi che nel tenant sia installato Azure AD Basic o Premium.
4. Nella finestra che appare completare le opzioni come segue: 
 * **Nome**: qualsiasi valore desiderato, ad esempio _SharePoint_.
 * **URL interno**: si tratta dell'URL del sito di SharePoint nella forma interna, ad esempio https://SharePoint/. In questo esempio assicurarsi di usare https.
 * In **Metodo di autenticazione preliminare** fare clic su _Azure Active Directory_.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. Dopo la pubblicazione dell'app fare clic sulla scheda **Configura**.
6. Scorrere fino all'opzione **Convertire l'URL nelle intestazioni**. Il valore predefinito è _Sì_, cambiarlo in _No_. 

 SharePoint usa il valore _Intestazione host_ per eseguire ricerche nel sito e genera anche collegamenti basati su questo valore. In questo modo si garantisce che qualsiasi collegamento generato da SharePoint sia un URL pubblicato impostato correttamente per usare l'URL esterno. Impostando il valore su _Sì_ si consente anche al connettore di inoltrare la richiesta all'applicazione back-end. Impostando invece il valore su _No_ il connettore non invierà il nome host interno e invierà invece l'intestazione host come URL pubblicato per l'applicazione back-end.

 Inoltre, per garantire che SharePoint accetti questo URL, è necessario completare un'ulteriore configurazione nel server SharePoint. Lo si farà nella sezione successiva.

7. Cambiare **Metodo di autenticazione interno** in _Integrata di Windows_. Se il tenant Azure AD usa un UPN diverso nel cloud rispetto a quello locale, aggiornare anche l'**Identità di accesso delegata**.
8. Impostare **Nome dell'entità servizio dell'applicazione interna** sul valore impostato in precedenza. Ad esempio _http/sharepoint.demo.o365identity.us_.
9. Ora è possibile assegnare l'applicazione agli utenti di destinazione.

L'applicazione dovrebbe essere simile alla seguente:

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

###<a name="part-3-ensure-sharepoint-knows-about-the-external-url"></a>Parte 3: Assicurarsi che SharePoint sia al corrente dell'URL esterno

L'ultimo passaggio da eseguire è assicurarsi che SharePoint possa trovare il sito in base all'URL esterno, in modo che possa sviluppare i collegamenti in base a tale URL esterno. Si procede configurando mapping di accesso alternativi per SharePoint.

**Configurare un nome alternativo per il sito di SharePoint**

1. Aprire il sito **Amministrazione centrale SharePoint 2013**.
2. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. 

 Verrà visualizzata la casella **Mapping di accesso alternativo**.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. Nell'elenco a discesa accanto a **Raccolte di mapping di accesso alternativo** selezionare **Cambia raccolta di mapping di accesso alternativo**.
4. Selezionare il sito, ad esempio **SharePoint - 80**.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. È possibile scegliere di aggiungere l'URL pubblicato come URL interno o URL pubblico. Questo esempio usa un **URL pubblico** come extranet.
6. Fare clic su **Modifica URL pubblici** nel percorso **Extranet**, quindi immettere il percorso in cui si pubblica l'applicazione, come nel passaggio precedente. Ad esempio _https://sharepoint-iddemo.msappproxy.net_.

  ![Connettori del proxy applicazione AzureAD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. Fare clic su **Save**. 

 È ora possibile accedere al sito di SharePoint esternamente tramite il proxy applicazione Azure AD.

##<a name="next-steps"></a>Passaggi successivi

[Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md)<br>
[Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md)<br>
[Pubblicazione di SharePoint 2016 e Office Online Server con il proxy applicazione Azure AD](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)









<!--HONumber=Feb17_HO1-->


