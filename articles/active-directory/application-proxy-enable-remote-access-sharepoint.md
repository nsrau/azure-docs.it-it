---
title: Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD | Microsoft Docs
description: Offre le informazioni di base necessarie per integrare un server SharePoint locale con il proxy di applicazione di Azure AD.
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
ms.date: 09/06/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 2b4ad3e7bda1346e606b2c185c204154b8f19f87
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD

Questo articolo illustra come integrare un server SharePoint locale con il proxy di applicazione di Azure Active Directory (Azure AD).

Per abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD, seguire le istruzioni dettagliate riportate nelle sezioni di questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che SharePoint 2013 o versione successiva sia già installato nell'ambiente. Tenere in considerazione anche i seguenti prerequisiti:

* SharePoint include il supporto nativo di Kerberos. Di conseguenza, gli utenti che accedono in remoto ai siti interni tramite il proxy di applicazione di Azure AD possono presumere di essere in possesso dell'esperienza Single Sign-On.

* Questo scenario include le modifiche di configurazione per il server SharePoint. Si consiglia di usare un ambiente di gestione temporanea. In questo modo, è possibile apportare aggiornamenti al server di gestione temporanea e quindi facilitare un ciclo di test prima del passaggio in produzione.

* Nell'URL pubblicato è necessario SSL. Per garantire che i collegamenti vengano inviati e mappati correttamente, è necessario che SSL sia abilitato anche nel sito interno. Se SSL non è ancora stato configurato, vedere [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) (Configurare SSL per SharePoint 2013) per le necessarie istruzioni. Inoltre assicurarsi che il computer di connessione consideri attendibile il certificato che si emette (non deve essere necessariamente un certificato rilasciato pubblicamente).

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Passaggio 1: Configurare l'accesso Single Sign-On a SharePoint

Per le applicazioni locali che usano l'autenticazione di Windows, è possibile ottenere l'accesso Single Sign-On (SSO) con il protocollo di autenticazione Kerberos e una funzionalità chiamata delega vincolata Kerberos. Se configurata, questa funzionalità consente al connettore proxy di applicazione di ottenere un token di Windows per un utente, anche se questi non ha eseguito l'accesso diretto a Windows. Per altre informazioni sulla delega vincolata Kerberos, vedere [Panoramica della delega vincolata Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Per configurare la delega vincolata Kerberos per un server SharePoint, usare le procedure descritte nelle sezioni seguenti:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Verificare che SharePoint sia in esecuzione in un account di servizio

Verificare che SharePoint sia in esecuzione in un account di servizio definito, non in un sistema locale, un servizio locale o un servizio di rete. Eseguire questa operazione in modo da poter associare i nomi di entità servizio (SPN) a un account valido. I nomi SPN sono usati dal protocollo Kerberos per identificare i diversi servizi e saranno necessari in seguito per configurare la delega vincolata Kerberos.

> [!NOTE]
È necessario avere un account di Azure AD creato in precedenza per il servizio. Si consiglia di consentire la modifica automatica della password. Per altre informazioni sull'insieme completo di passaggi e sulla risoluzione di problemi, vedere [Configurare la modifica automatica della password in SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Per garantire che i siti siano in esecuzione in un account di servizio definito, seguire questa procedura:

1. Aprire il sito **Amministrazione centrale SharePoint 2013**.
2. Passare a **Sicurezza** e selezionare **Configura account di servizio**.
3. Selezionare **Pool di applicazioni Web - SharePoint - 80**. È possibile che le opzioni siano leggermente diverse, a seconda del nome del pool Web o se il pool Web usa SSL per impostazione predefinita.

  ![Opzioni per la configurazione di un account di servizio](./media/application-proxy-remote-sharepoint/service-web-application.png)

4. Se il campo **Selezionare un account per il componente** è impostato su **Servizio locale** o **Servizio di rete**, è necessario creare un account. In caso contrario, l'operazione è terminata ed è possibile procedere alla sezione successiva.
5. Selezionare **Registra nuovo account gestito**. Dopo aver creato l'account, prima di poterlo usare è necessario impostare l'opzione **Pool di applicazioni Web**.

### <a name="configure-sharepoint-for-kerberos"></a>Configurare SharePoint per Kerberos

Usare la delega vincolata Kerberos per eseguire l'accesso Single Sign-On al server SharePoint.

Per configurare il sito di SharePoint per l'autenticazione Kerberos:

1. Aprire il sito **Amministrazione centrale SharePoint 2013**.
2. Passare a **Gestione applicazioni**, selezionare **Gestisci applicazioni Web** e selezionare il sito di SharePoint. In questo esempio si tratta di **SharePoint - 80**.

  ![Selezione del sito di SharePoint](./media/application-proxy-remote-sharepoint/manage-web-applications.png)

3. Fare clic su **Provider di autenticazione** sulla barra degli strumenti.
4. Nella casella **Provider di autenticazione** fare clic su **Area predefinita** per visualizzare le impostazioni.
5. Nella finestra di dialogo **Modifica autenticazione** scorrere verso il basso fino a visualizzare **Tipi di autenticazione delle attestazioni**. Assicurarsi che le opzioni **Abilita autenticazione di Windows** e **Autenticazione integrata di Windows** siano entrambe selezionate.
6. Nella casella di riepilogo a discesa per il campo Autenticazione integrata di Windows verificare che l'opzione **Negozia (Kerberos)** sia selezionata.

  ![Finestra di dialogo Modifica autenticazione](./media/application-proxy-remote-sharepoint/service-edit-authentication.png)

7. Nella parte inferiore della finestra di dialogo **Modifica autenticazione** fare clic su **Salva**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Impostare un nome dell'entità servizio per l'account del servizio SharePoint

Prima di configurare la delega vincolata Kerberos, è necessario identificare il servizio SharePoint in esecuzione come account del servizio configurato. Identificare il servizio impostando un SPN. Per altre informazioni vedere [Service Principal Names](https://technet.microsoft.com/library/cc961723.aspx) (Nomi di entità servizio).

Il formato del nome SPN è:

```
<service class>/<host>:<port>
```

Nel formato SPN:

* La _classe di servizio_ è un nome univoco per il servizio. Per SharePoint si usa **HTTP**.

* L'_host_ è il nome di dominio completo o il nome Netbios dell'host in cui il servizio è in esecuzione. Per un sito di SharePoint può essere necessario questo testo sia l'URL del sito, a seconda della versione di IIS in uso.

* La _porta_ è opzionale.

Se il nome di dominio completo del server SharePoint è:

```
sharepoint.demo.o365identity.us
```

Il nome SPN sarà:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Potrebbe essere necessario anche impostare nomi SPN per siti specifici sul server. Per altre informazioni, vedere [Configurare l'autenticazione Kerberos](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Leggere con attenzione la sezione "Creare nomi delle entità servizio per applicazioni Web mediante l'autenticazione Kerberos".

Il modo più semplice per impostare nomi SPN è quello di seguire i formati dei nomi SPN già presenti per altri siti. Copiare i nomi SPN per la registrazione con l'account del servizio. A tale scopo, seguire questa procedura:

1. Passare al sito con il nome SPN da un altro computer.
 Quando si esegue questa operazione, il gruppo di ticket Kerberos pertinente viene memorizzato nella cache del computer. Questi ticket contengono il nome SPN del sito di destinazione a cui si è passati.

2. È possibile estrarre il nome SPN per il sito usando uno strumento denominato [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). In una finestra di comando in esecuzione nello stesso contesto dell'utente che ha eseguito l'accesso al sito nel browser eseguire il comando seguente:
```
Klist
```
Klist restituisce quindi il set di nomi SPN di destinazione. In questo esempio il valore evidenziato è il nome SPN necessario:

  ![Risultati Klist di esempio](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Dopo aver ottenuto l'SPN, assicurarsi che sia configurato correttamente per l'account del servizio impostato in precedenza per l'applicazione Web. Eseguire il comando seguente dal prompt dei comandi come amministratore del dominio:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Questo comando imposta il nome SPN per l'account del servizio SharePoint in esecuzione come _demo\sp_svc_.

 Sostituire _http/sharepoint.demo.o365identity.us_ con il nome SPN per il server e _demo\sp_svc_ con l'account del servizio nel proprio ambiente. Il comando Setspn cerca il nome SPN prima di aggiungerlo. In questo caso è possibile che venga restituito un errore di **valore SPN duplicato**. Se viene visualizzato questo errore, assicurarsi che il valore sia associato con l'account del servizio.

È possibile verificare che il nome SPN sia stato aggiunto tramite il comando Setspn con l'opzione -l. Per altre informazioni su questo comando, vedere [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Verificare che il connettore sia impostato come delegato attendibile a SharePoint

Configurare la delega vincolata Kerberos in modo che il servizio proxy di applicazione di Azure AD possa delegare le identità utente al servizio SharePoint. A questo scopo, consentire al connettore proxy di applicazione di recuperare i ticket Kerberos per gli utenti autenticati in Azure AD. Quindi il server passa il contesto all'applicazione di destinazione o, in questo caso, a SharePoint.

Per configurare la delega vincolata Kerberos, ripetere i passaggi seguenti per ogni computer di connessione:

1. Eseguire l'accesso come amministratore a un controller di dominio e quindi aprire un dominio **Utenti e computer di Active Directory**.
2. Trovare il computer in cui è in esecuzione il connettore. In questo esempio è lo stesso server SharePoint.
3. Fare doppio clic sul computer e quindi sulla scheda **Delega**.
4. Assicurarsi che la delega sia impostata su **Computer attendibile per la delega solo ai servizi specificati**. Selezionare quindi **Usa un qualsiasi protocollo di autenticazione**.

  ![Impostazioni di delega](./media/application-proxy-remote-sharepoint/delegation-box.png)

5. Fare clic sul pulsante **Aggiungi** e quindi su **Utenti o computer**e individuare l'account del servizio.

  ![Aggiungere il nome SPN per l'account del servizio](./media/application-proxy-remote-sharepoint/users-computers.png)

6. Nell'elenco di nomi SPN selezionare quello creato in precedenza per l'account del servizio.
7. Fare clic su **OK**. Fare nuovamente clic su **OK** per salvare le modifiche.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Passaggio 2: Abilitare l'accesso remoto a SharePoint

Dopo aver abilitato SharePoint per Kerberos e aver configurato la delega vincolata Kerberos, è possibile pubblicare la farm di SharePoint per l'accesso remoto tramite il proxy di applicazione di Azure AD.

1. Pubblicare il sito di SharePoint con le impostazioni seguenti. Per istruzioni dettagliate, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md). 
   - **URL interno**: URL del sito di SharePoint nella sua forma interna, ad esempio **https://SharePoint/**. In questo esempio assicurarsi di usare **https**
   - **Metodo di autenticazione preliminare**: Azure Active Directory
   - **Convertire l'URL nelle intestazioni**: NO

   >[!TIP]
   >SharePoint usa il valore _Intestazione host_ per cercare il sito e, in base a questo valore, genera i collegamenti. Di conseguenza, qualsiasi collegamento generato da SharePoint è un URL pubblicato e impostato correttamente per l'uso dell'URL esterno. Impostando il valore su **SÌ** si consente al connettore di inoltrare la richiesta all'applicazione back-end. Se invece si imposta il valore su **NO**, il connettore non invierà il nome host interno, ma invia l'intestazione host come URL pubblicato per l'applicazione back-end.

   ![Pubblicare SharePoint come applicazione](./media/application-proxy-remote-sharepoint/publish-app.png)

2. Dopo la pubblicazione dell'app, configurare le impostazioni Single Sign-On eseguendo le operazioni seguenti:

   1. Nella pagina dell'applicazione nel portale selezionare **Single Sign-On**.
   2. Per la modalità Single Sign-On, selezionare **Autenticazione integrata di Windows**.
   3. Impostare l'SPN dell'applicazione interna sul valore configurato in precedenza. Per questo esempio, il valore è **http/sharepoint.demo.o365identity.us**.

   ![Configurare l'autenticazione integrata di Windows per SSO](./media/application-proxy-remote-sharepoint/configure-iwa.png)

3. Per completare la configurazione dell'applicazione, passare alla sezione **Utenti e gruppi** e specificare gli utenti autorizzati ad accedere all'applicazione. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Passaggio 3: Assicurarsi che SharePoint sia al corrente dell'URL esterno

L'ultimo passaggio consiste nell'accertarsi che SharePoint possa trovare il sito in base all'URL esterno, in modo da poter sviluppare i collegamenti in base a questo URL. A questo scopo, si configurano mapping di accesso alternativo per SharePoint.

1. Aprire il sito **Amministrazione centrale SharePoint 2013**.
2. In **Impostazioni di sistema** selezionare **Configura mapping di accesso alternativo**. Verrà visualizzata la casella Mapping di accesso alternativo.

  ![Casella Mapping di accesso alternativo](./media/application-proxy-remote-sharepoint/alternate-access1.png)

3. Nell'elenco a discesa accanto a **Raccolta di mapping di accesso alternativo** selezionare **Change Alternate Access Mapping Collection** (Cambia raccolta di mapping di accesso alternativo).
4. Selezionare il sito, ad esempio **SharePoint - 80**.
5. È possibile scegliere di aggiungere l'URL pubblicato come URL interno o URL pubblico. Questo esempio usa un URL pubblico come extranet.
6. Fare clic su **Modifica URL pubblici** nel percorso **Extranet** e quindi immettere l'URL esterno creato durante la pubblicazione dell'applicazione. Ad esempio: **https://sharepoint-iddemo.msappproxy.net**.

  ![Definizione del percorso](./media/application-proxy-remote-sharepoint/alternate-access3.png)

7. Fare clic su **Save**.

È ora possibile accedere al sito di SharePoint esternamente tramite il proxy di applicazione di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Uso di domini personalizzati nel proxy di applicazione Azure AD](active-directory-application-proxy-custom-domains.md)
- [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)


