---
title: Installare un gateway dati locale
description: Prima di poter accedere ai dati in locale delle App per la logica di Azure, è necessario scaricare e installare il gateway dati locale
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673825"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installare il gateway dati locale per App per la logica di Azure

Prima di [connettersi a origini dati locali da App per la logica](../logic-apps/logic-apps-gateway-connection.md)di Azure , scaricare e installare il gateway dati locale [in](https://aka.ms/on-premises-data-gateway-installer) un computer locale. Il gateway funziona come un bridge che fornisce il trasferimento rapido dei dati e la crittografia tra le origini dati in locale e le app per la logica. È possibile usare la stessa installazione del gateway con altri servizi cloud, ad esempio Power BI, Power Automate, Power Apps e Azure Analysis Services.You can use the same gateway installation with other cloud services, such as Power BI, Power Automate, Power Apps, and Azure Analysis Services. Per informazioni su come usare il gateway con questi servizi, vedere questi articoli:For information about how to use the gateway with these services, see these articles:

* [Gateway dati locale di Microsoft Power Automate](/power-automate/gateway-reference)
* [Gateway dati locale di Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway dati locale di Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Questo articolo illustra come scaricare, installare e configurare il gateway dati locale in modo da poter accedere alle origini dati locali da App per la logica di Azure.This article shows how to download, install, and set up your on-premises data gateway so that you can access on-premises data sources from Azure Logic Apps. Per altre informazioni sul [funzionamento del gateway dati](#gateway-cloud-service) più avanti in questo argomento, vedere Più avanti. Per altre informazioni sul gateway, vedere [Che cos'è un gateway locale?](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem) Per automatizzare le attività di installazione e gestione del gateway, visitare la raccolta di PowerShell per i [cmdlet di PowerShell per DataGateway.](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si dispone di un account Azure con una sottoscrizione, [iscriversi per ottenere un account Azure gratuito.](https://azure.microsoft.com/free/)

  * L'account Azure deve appartenere a un singolo tenant o directory di [Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) È necessario usare lo stesso account di Azure per installare e amministrare il gateway nel computer locale.

  * Durante l'installazione del gateway, si accede con l'account Azure, che collega l'installazione del gateway all'account Azure e solo a tale account. Successivamente, nel portale di Azure, è necessario usare lo stesso account di Azure e lo stesso tenant di Azure AD quando si crea una risorsa gateway di Azure che registra e rivendica l'installazione del gateway. Nelle app per la logica di Azure i trigger e le azioni locali usano quindi la risorsa gateway per la connessione alle origini dati locali.

    > [!NOTE]
    > È possibile collegare tra loro una sola installazione gateway e una risorsa gateway di Azure.You can link only one gateway installation and one Azure gateway resource to each other. Non è possibile collegare la stessa installazione del gateway a più account di Azure o a risorse gateway di Azure.You can't link the same gateway installation to multiple Azure accounts or Azure gateway resources. Tuttavia, un account di Azure può collegarsi a più installazioni gateway e risorse gateway di Azure.However, an Azure account can link to multiple gateway installations and Azure gateway resources. In un trigger o un'azione locale è possibile selezionare tra le varie sottoscrizioni di Azure e quindi selezionare una risorsa gateway associata.

  * È necessario accedere con un account aziendale o dell'istituto di istruzione, noto anche come account *dell'organizzazione,* che ha l'aspetto `username@contoso.com`di . Non è possibile usare account B2B (guest) di @hotmail.com Azure @outlook.como account Microsoft personali, ad esempio o .

    > [!TIP]
    > Se ti sei iscritto a un'offerta di Office 365 e non `username@domain.onmicrosoft.com`hai fornito il tuo indirizzo e-mail di lavoro, il tuo indirizzo potrebbe essere simile a . L'account viene archiviato in un tenant in Azure Active Directory (Azure AD). Nella maggior parte dei casi, il nome dell'entità utente (UPN) per l'account Azure AD corrisponde all'indirizzo di posta elettronica.
    >
    > Per usare una sottoscrizione standard di [Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) collegata a un account Microsoft, creare innanzitutto un tenant in Azure [AD](../active-directory/develop/quickstart-create-new-tenant.md) o usare la directory predefinita. Aggiungere un utente con una password alla directory e quindi concedere a tale utente l'accesso alla sottoscrizione di Azure.Add a user with a password to the directory, and then give that user access to your Azure subscription. Sarà quindi possibile accedere durante l'installazione del gateway con questo nome utente e questa password.

* Di seguito sono riportati i requisiti per il computer locale:

  **Requisiti minimi**

  * .NET Framework 4.7.2
  * Windows 7 versione a 64 bit o Windows Server 2008 R2 (o versione successiva)

  **Requisiti consigliati**

  * CPU 8 core
  * 8 GB di memoria
  * Versione a 64 bit di Windows Server 2012 R2 o versione successiva
  * Archiviazione unità a stato solido (SSD) per lo spooling

  > [!NOTE]
  > Il gateway non supporta Windows Server Core.

* **Considerazioni correlate**

  * Installare il gateway dati locale solo in un computer locale, non in un controller di dominio. Non è necessario installare il gateway nello stesso computer dell'origine dati. È necessario un solo gateway per tutte le origini dati, pertanto non è necessario installare il gateway per ogni origine dati.

    > [!TIP]
    > Per ridurre al minimo la latenza, è possibile installare il gateway il più vicino possibile all'origine dati o nello stesso computer, presupponendo che si disponga delle autorizzazioni.

  * Installare il gateway in un computer che si trovano in una rete cablata, connesso a Internet, sempre acceso e non passa alla modalità di sospensione. In caso contrario, il gateway non può essere eseguito e le prestazioni potrebbero risentire di una rete wireless.

  * Se si prevede di utilizzare l'autenticazione di Windows, assicurarsi di installare il gateway in un computer membro dello stesso ambiente Active Directory delle origini dati.

  * L'area selezionata per l'installazione del gateway è la stessa posizione che è necessario selezionare quando in seguito si crea la risorsa gateway di Azure per l'app per la logica. Per impostazione predefinita, questa area è la stessa posizione del tenant di Azure AD che gestisce l'account di Azure.By default, this region is the same location as your Azure AD tenant that manages your Azure account. Tuttavia, è possibile modificare il percorso durante l'installazione del gateway.

  * Se si sta aggiornando l'installazione del gateway alla versione più recente, disinstallare prima il gateway corrente per un'esperienza più pulita.

  * Il gateway ha due modalità: modalità standard e modalità personale, che si applica solo a Power BI. Non è possibile avere più gateway in esecuzione nella stessa modalità nello stesso computer.

  * Le app per la logica di Azure supportano le operazioni di lettura e scrittura tramite il gateway. Tuttavia, queste operazioni hanno [limiti sulla loro dimensione del payload](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installare il gateway dati

1. [Scaricare ed eseguire il programma di installazione di gateway in un computer locale](https://aka.ms/on-premises-data-gateway-installer).

1. Esaminare i requisiti minimi, mantenere il percorso di installazione predefinito, accettare le condizioni per l'utilizzo e quindi selezionare **Installa**.

   ![Rivedere i requisiti e accettare le condizioni per l'utilizzo](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Dopo aver installato correttamente il gateway, specificare l'indirizzo di posta elettronica per l'account Azure e quindi selezionare **Accedi**, ad esempio:

   ![Accedere con l'account aziendale o dell'istituto di istruzione](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   L'installazione del gateway può essere collegata a un solo account Azure.Your gateway installation can link to only one Azure account.

1. Selezionare **Registra un nuovo gateway su questo computer** > **Avanti**. Questo passaggio consente di registrare l'installazione del gateway con il [servizio cloud gateway.](#gateway-cloud-service)

   ![Registrare il gateway nel computer locale](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Specificare le informazioni seguenti per l'installazione del gateway:

   * Un nome di gateway univoco nel tenant di Azure ADA gateway name that's unique across your Azure AD tenant
   * La chiave di ripristino, che deve avere almeno otto caratteri, che si desidera utilizzare
   * Conferma per la chiave di ripristino

   ![Fornire informazioni per l'installazione del gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Salvare e conservare la chiave di ripristino in un luogo sicuro. Questa chiave è necessaria se si desidera modificare la posizione, spostare, ripristinare o assumere il controllo dell'installazione di un gateway.

   Si noti l'opzione Aggiungi a un cluster di **gateway esistente,** che è possibile selezionare quando si installano gateway aggiuntivi per scenari a [disponibilità elevata.](#high-availability)

1. Controllare l'area per il servizio cloud gateway e il bus di servizio di [Azure](https://azure.microsoft.com/services/service-bus/) usati dall'installazione del gateway. Per impostazione predefinita, questa area è la stessa posizione del tenant di Azure AD per l'account Azure.By default, this region is the same location as the Azure AD tenant for your Azure account.

   ![Verificare l'area per il servizio gateway e il bus di servizioConfirm region for gateway service and service bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Per accettare l'area predefinita, selezionare **Configura**. Tuttavia, se l'area predefinita non è quella più vicina a te, puoi modificare l'area.

   *Perché modificare l'area per l'installazione del gateway?*

   Ad esempio, per ridurre la latenza, è possibile modificare l'area del gateway nella stessa area dell'app per la logica. In alternativa, è possibile selezionare l'area più vicina all'origine dati locale. La *risorsa per il gateway in Azure* e l'app per la logica possono avere posizioni diverse.

   1. Accanto all'area corrente, selezionare **Cambia area**.

      ![Modificare l'area del gateway corrente](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Nella pagina successiva aprire l'elenco **Seleziona area,** selezionare l'area desiderata e scegliere **Fatto**.

      ![Selezionare un'altra area per il servizio gatewaySelect another region for gateway service](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Rivedere le informazioni nella finestra di conferma finale. Questo esempio usa lo stesso account per app per la logica, Power BI, Power Apps e Power Automate, pertanto il gateway è disponibile per tutti questi servizi. Quando si è pronti, selezionare **Chiudi**.

   ![Confermare le informazioni sul gateway dati](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Creare ora [la risorsa di Azure per l'installazione](../logic-apps/logic-apps-gateway-connection.md)del gateway.

## <a name="check-or-adjust-communication-settings"></a>Controllare o regolare le impostazioni di comunicazione

Il gateway dati locale dipende dal bus di servizio di [Azure](../service-bus-messaging/service-bus-messaging-overview.md) per la connettività cloud e stabilisce le connessioni in uscita corrispondenti all'area di Azure associata al gateway. Se l'ambiente di lavoro richiede che il traffico passi attraverso un proxy o un firewall per accedere a Internet, questa restrizione potrebbe impedire al gateway dati locale di connettersi al servizio cloud gateway e al bus di servizio di Azure.If your work environment requires that traffic through a proxy or firewall to access the internet, this restriction might prevent the on-premises data gateway from connecting to the gateway cloud service and Azure Service Bus. Il gateway ha diverse impostazioni di comunicazione, che è possibile regolare. Per altre informazioni, vedere gli argomenti seguenti:

* [Modificare le impostazioni di comunicazione per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Configurare le impostazioni del proxy per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Supporto per la disponibilità elevata

Per evitare singoli punti di errore per l'accesso ai dati locali, è possibile avere più installazioni gateway (solo in modalità standard) con ognuna in un computer diverso e configurarle come cluster o gruppo. In questo modo, se il gateway primario non è disponibile, le richieste di dati vengono instradate al secondo gateway e così via. Poiché è possibile installare un solo gateway standard in un computer, è necessario installare ogni gateway aggiuntivo presente nel cluster in un computer diverso. Tutti i connettori che funzionano con il gateway dati locale supportano la disponibilità elevata.

* È necessario avere almeno un'installazione gateway con lo stesso account di Azure del gateway primario e la chiave di ripristino per tale installazione.

* Il gateway primario deve eseguire l'aggiornamento del gateway di novembre 2017 o versione successiva.

Dopo aver configurato il gateway primario, quando si passa all'installazione di un altro gateway selezionare **Aggiungi a un cluster**di gateway esistente , selezionare il gateway primario, ovvero il primo gateway installato, e fornire la chiave di ripristino per tale gateway. Per altre informazioni, vedere [Cluster a disponibilità elevata per il gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Modificare la posizione, eseguire la migrazione, ripristinare o sostituire un gateway esistente

Per modificare la posizione del gateway, spostare l'installazione del gateway in un nuovo computer, ripristinare un gateway danneggiato o assumere la proprietà di un gateway esistente, è necessaria la chiave di ripristino che è stata specificata durante l'installazione del gateway.

1. Eseguire il programma di installazione del gateway nel computer che dispone del gateway esistente. Se non si dispone del programma di installazione del gateway più recente, [scaricare la versione più recente del gateway.](https://aka.ms/on-premises-data-gateway-installer)

   > [!NOTE]
   > Prima di ripristinare il gateway nel computer in cui è installata l'installazione del gateway originale, è necessario disinstallare il gateway in tale computer. Questa azione disconnette il gateway originale.
   > Se si rimuove o si elimina un cluster gateway per qualsiasi servizio cloud, non è possibile ripristinarlo.

1. Dopo l'apertura del programma di installazione, accedere con lo stesso account di Azure usato per installare il gateway.

1. Selezionare **Migra, ripristina o acquisizione di un gateway** > esistente**Avanti**, ad esempio:

   ![Selezionare "Eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selezionare uno dei cluster e dei gateway disponibili e immettere la chiave di ripristino per il gateway selezionato, ad esempio:

   ![Selezionare il gateway e fornire la chiave di ripristinoSelect gateway and provide recovery key](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Per modificare l'area, selezionare **Cambia regione**e selezionare la nuova area.

1. Quando si è pronti, **selezionare Configura** in modo da poter completare l'attività.

## <a name="tenant-level-administration"></a>Amministrazione a livello di tenant

Per ottenere visibilità su tutti i gateway dati locali in un tenant di Azure AD, gli amministratori globali in tale tenant possono accedere all'interfaccia di amministrazione di [Power Platform](https://powerplatform.microsoft.com) come amministratore tenant e selezionare l'opzione **Gateway dati.** Per ulteriori informazioni, vedere Amministrazione a [livello di tenant per il gateway dati locale.](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Riavviare il gateway

Per impostazione predefinita, l'installazione del gateway nel computer locale viene eseguita come account del servizio Windows denominato "Servizio gateway dati locale". Tuttavia, l'installazione `NT SERVICE\PBIEgwService` del gateway utilizza il nome per le credenziali dell'account "Accedi come" e dispone delle autorizzazioni "Accedi come servizio".

> [!NOTE]
> L'account del servizio Windows è diverso dall'account usato per la connessione alle origini dati locali e dall'account di Azure usato quando si accede ai servizi cloud.

Come qualsiasi altro servizio Windows, è possibile avviare e arrestare il gateway in vari modi. Per ulteriori informazioni, vedere [Riavviare un gateway dati locale.](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Funzionamento del gateway

Gli utenti dell'organizzazione possono accedere ai dati locali per i quali dispongono già dell'accesso autorizzato. Tuttavia, prima che questi utenti possano connettersi all'origine dati locale, è necessario installare e configurare un gateway dati locale. In genere, un amministratore è la persona che installa e configura un gateway. Queste azioni potrebbero richiedere autorizzazioni di amministratore del server o conoscenze speciali sui server locali.

Il gateway consente di facilitare una comunicazione dietro le quinte più veloce e sicura. Questa comunicazione passa tra un utente nel cloud, il servizio cloud gateway e l'origine dati locale. Il servizio cloud gateway crittografa e archivia le credenziali dell'origine dati e i dettagli del gateway. Il servizio instrada anche le query e i relativi risultati tra l'utente, il gateway e l'origine dati locale.

Il gateway funziona con i firewall e usa solo connessioni in uscita. Tutto il traffico ha origine come traffico in uscita protetto dall'agente gateway. Il gateway inoltra i dati da origini locali su canali crittografati tramite il bus di servizio di [Azure.](../service-bus-messaging/service-bus-messaging-overview.md) Il bus di servizio crea un canale tra il gateway e il servizio chiamante, ma non archivia alcun dato. Tutti i dati che attraversano il gateway sono crittografati.

![Architettura per il gateway dati locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> A seconda del servizio cloud, potrebbe essere necessario configurare un'origine dati per il gateway.

Questi passaggi descrivono cosa accade quando si interagisce con un elemento connesso a un'origine dati locale:These steps describe what happens when you interact with an element that's connected to an on-premises data source:

1. Il servizio cloud crea una query, insieme alle credenziali crittografate per l'origine dati. Il servizio invia quindi la query e le credenziali alla coda del gateway per l'elaborazione.

1. The gateway cloud service analyzes the query and pushes the request to Azure Service Bus.

1. Il bus di servizio di Azure invia le richieste in sospeso al gateway.

1. Il gateway ottiene la query, decrittografa le credenziali e si connette a una o più origini dati con tali credenziali.

1. Il gateway invia la query all'origine dati per l'esecuzione.

1. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al servizio cloud del gateway. Infine, il servizio cloud del gateway usa i risultati.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticazione a origini dati locali

Una credenziale archiviata viene utilizzata per connettersi dal gateway alle origini dati locali. Indipendentemente dall'utente, il gateway utilizza le credenziali archiviate per la connessione. Potrebbero esserci eccezioni di autenticazione per servizi specifici, ad esempio DirectQuery e LiveConnect per Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

I servizi cloud Microsoft usano [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) per autenticare gli utenti. Un tenant di Azure AD contiene nomi utente e gruppi di sicurezza. In genere, l'indirizzo di posta elettronica utilizzato per l'accesso corrisponde al nome dell'entità utente (UPN) per l'account.

### <a name="what-is-my-upn"></a>Che cos'è il mio UPN?

Se non sei un amministratore di dominio, potresti non conoscere il tuo UPN. Per trovare l'UPN per `whoami /upn` l'account, eseguire il comando dalla workstation. Anche se il risultato è simile a un indirizzo di posta elettronica, il risultato è l'UPN per l'account di dominio locale.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizzare un'istanza di Active Directory locale con Azure AD

L'UPN per gli account Active Directory locali e gli account Azure AD deve essere lo stesso. Quindi, assicurarsi che ogni account di Active Directory locale corrisponda all'account azure AD. I servizi cloud conoscono solo gli account in Azure AD. Pertanto, non è necessario aggiungere un account ad Active Directory locale. Se l'account non esiste in Azure AD, non è possibile usare tale account.

Ecco i modi in cui è possibile associare gli account di Active Directory locali ad Azure AD.

* Aggiungere manualmente gli account ad Azure AD.

  Creare un account nel portale di Azure o nell'interfaccia di amministrazione di Microsoft 365.Create an account in the Azure portal or in the Microsoft 365 admin center. Assicurarsi che il nome dell'account corrisponda all'UPN dell'account di Active Directory locale.

* Sincronizzare gli account locali con il tenant di Azure AD usando lo strumento Azure Active Directory Connect.Synchronize local accounts to your Azure AD tenant by using the Azure Active Directory Connect tool.

  Lo strumento Azure AD Connect offre opzioni per la sincronizzazione della directory e la configurazione dell'autenticazione. Queste opzioni includono la sincronizzazione dell'hash delle password, l'autenticazione pass-through e la federazione. Se non si è un amministratore tenant o un amministratore di dominio locale, contattare l'amministratore IT per configurare Azure AD Connect.If you're not a tenant admin or a local domain admin, contact your IT admin to get Azure AD Connect set up. Azure AD Connect garantisce che l'UPN di Azure AD corrisponda all'UPN di Active Directory locale. Questa corrispondenza è utile se si usano connessioni live di Analysis Services Con Power BI o funzionalità Single Sign-On (SSO).

  > [!NOTE]
  > La sincronizzazione degli account con lo strumento Azure AD Connect crea nuovi account nel tenant di Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Domande frequenti e risoluzione dei problemi

Per altre informazioni, vedere gli argomenti seguenti:

* [Domande frequenti sul gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Risolvere i problemi del gateway dati locale](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorare e ottimizzare le prestazioni del gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai dati locali dalle app per la logicaConnect to on-premises data from logic apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funzionalità di Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connettori per App per la logica di Azure](../connectors/apis-list.md)
