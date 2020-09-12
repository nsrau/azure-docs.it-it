---
title: Installare un gateway dati locale
description: Prima di poter accedere ai dati in locale delle App per la logica di Azure, è necessario scaricare e installare il gateway dati locale
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: f646af4cad6101e019e58f4f50a40b07aff19461
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660483"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installare il gateway dati locale per App per la logica di Azure

Prima di poter [connettere le origini dati locali da App per la logica di Azure](../logic-apps/logic-apps-gateway-connection.md), scaricare e installare il [gateway dati locale](https://aka.ms/on-premises-data-gateway-installer) in un computer locale. Il gateway funge da ponte che consente il rapido trasferimento dei dati e la crittografia tra le origini di dati in locale e le app per la logica. È possibile usare la stessa installazione del gateway con altri servizi cloud come Power BI, Power Automate, Power Apps e Azure Analysis Services. Per informazioni su come usare il gateway con questi servizi, consultare gli articoli seguenti:

* [Gateway dati locale di Microsoft Power Automate](/power-automate/gateway-reference)
* [Gateway dati locale di Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway dati locale di Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Questo articolo illustra come scaricare, installare e configurare il gateway dati locale in modo da poter accedere alle origini dati locali da App per la logica di Azure. È anche possibile ottenere altre informazioni sul [funzionamento del gateway dati](#gateway-cloud-service) più avanti in questo argomento. Per altre informazioni sul gateway, vedere [Cos'è un gateway dati locale](/data-integration/gateway/service-gateway-onprem)? Per automatizzare le attività di installazione e gestione del gateway, visitare la PowerShell Gallery per i [cmdlet di PowerShell DataGateway](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha un account Azure con una sottoscrizione, [iscriversi per crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * L'account di Azure deve essere un account aziendale o dell'Istituto di istruzione, simile a `username@contoso.com` . Non è possibile usare un account di Azure B2B (guest) o un account Microsoft personale, ad esempio @hotmail.com o @outlook.com.

    > [!NOTE]
    > Se è stata effettuata l'iscrizione per un'offerta di Microsoft 365 e non è stato fornito l'indirizzo di posta elettronica dell'ufficio, l'indirizzo potrebbe essere simile a `username@domain.onmicrosoft.com` . L'account è archiviato in un tenant di Azure AD. Nella maggior parte dei casi, il nome dell'entità utente (UPN) per l'account Azure è uguale a quello dell'indirizzo di posta elettronica.

    Per usare una [sottoscrizione standard di Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) associata a una account Microsoft, creare prima [un tenant Azure ad](../active-directory/develop/quickstart-create-new-tenant.md) o usare la directory predefinita. Aggiungere un utente con una password alla directory e quindi concedere all'utente l'accesso alla sottoscrizione di Azure. Sarà quindi possibile accedere durante l'installazione del gateway con questo nome utente e questa password.

  * L'account Azure deve appartenere solo a una singola [Directory o a un tenant di Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md#terminology). È necessario usare lo stesso account Azure per l'installazione e l'amministrazione del gateway nel computer locale.

  * Quando si installa il gateway, si accede con l'account Azure, che collega l'installazione del gateway al proprio account Azure e solo a tale account. Non è possibile collegare la stessa installazione del gateway tra più account Azure o Azure AD tenant.

  * Più avanti nel portale di Azure è necessario usare lo stesso account di Azure per creare una risorsa del gateway di Azure che si collega all'installazione del gateway. È possibile collegare tra loro solo un'installazione del gateway e una risorsa del gateway di Azure. Tuttavia, l'account di Azure può essere collegato a diverse installazioni del gateway associate a una risorsa del gateway di Azure. Le app per la logica possono quindi usare questa risorsa gateway in trigger e azioni che possono accedere alle origini dati locali.

* Di seguito sono riportati i requisiti per il computer locale:

  **Requisiti minimi**

  * .NET Framework 4.7.2
  * Windows 7 versione a 64 bit o Windows Server 2008 R2 (o versione successiva)

  **Requisiti consigliati**

  * CPU 8 core
  * 8 GB di memoria
  * Versione a 64 bit di Windows Server 2012 R2 o versione successiva
  * Archiviazione SSD per lo spooling

  > [!NOTE]
  > Il gateway non supporta Windows Server Core.

* **Considerazioni correlate**

  * Installare il gateway dati locale solo in un computer locale, non in un controller di dominio. Non è necessario installare il gateway nello stesso computer dell'origine dati. È necessario un solo gateway per tutte le origini dati, pertanto non occorre installare il gateway per ogni origine dati.

    > [!TIP]
    > Per ridurre al minimo la latenza, è possibile installare il gateway il più vicino possibile all'origine dati o nello stesso computer, presupponendo che si disponga delle autorizzazioni.

  * Installare il gateway in un computer locale che si trova in una rete cablata, connesso a Internet, sempre acceso e che non vada in sospensione. Il gateway non potrebbe essere eseguito altrimenti e le prestazioni potrebbero diminuire su una rete wireless.

  * Se si prevede di usare l'autenticazione di Windows, assicurarsi di installare il gateway in un computer membro dello stesso ambiente Active Directory delle origini dati.

  * L'area selezionata per l'installazione del gateway è la stessa che è necessario selezionare quando in seguito si crea la risorsa del gateway di Azure per l'app per la logica. Per impostazione predefinita, questa area è la stessa del tenant di Azure AD che gestisce l'account Azure. Tuttavia, è possibile modificare la posizione durante l'installazione del gateway.

  * Se si sta aggiornando l'installazione del gateway, disinstallare prima il gateway corrente per un'esperienza pulita.

    È consigliabile verificare che sia in uso una versione supportata. Microsoft rilascia ogni mese un nuovo aggiornamento per il gateway dati locale e attualmente supporta solo le ultime sei versioni per il gateway dati locale. Se si verificano problemi con la versione in uso, provare a eseguire [l'aggiornamento alla versione più recente](https://aka.ms/on-premises-data-gateway-installer) perché il problema potrebbe essere stato risolto nell'ultima versione.

  * Il gateway usa due modalità: la modalità standard e la modalità personale, che si applica solo a Power BI. Non è possibile eseguire più di un gateway nella stessa modalità sullo stesso computer.

  * App per la logica di Azure supporta le operazioni di lettura e scrittura tramite il gateway. Tuttavia, queste operazioni presentano [limiti di dimensioni del payload](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installare il gateway dati

1. [Scaricare ed eseguire il programma di installazione di gateway in un computer locale](https://aka.ms/on-premises-data-gateway-installer).

1. Verificare i requisiti minimi, mantenere il percorso di installazione predefinito, accettare le condizioni per l'utilizzo e quindi selezionare **Installa**.

   ![Esaminare i requisiti e accettare le condizioni per l'utilizzo](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Dopo aver installato correttamente il gateway, specificare l'indirizzo di posta elettronica per l'account Azure e quindi selezionare **Accedi**, ad esempio:

   ![Accedere con l'account aziendale o dell'istituto di istruzione](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   L'installazione del gateway può collegarsi a un solo account Azure.

1. Selezionare l'opzione **Consente di registrare un nuovo gateway in questo computer** > **Avanti**. Questo passaggio registra l'installazione del gateway con il [servizio cloud del gateway](#gateway-cloud-service).

   ![Registrare il gateway nel computer locale](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Specificare le informazioni seguenti per l'installazione del gateway:

   * Un nome del gateway univoco nel tenant di Azure AD
   * La chiave di ripristino che si desidera usare e che deve contenere almeno otto caratteri
   * Conferma per la chiave di ripristino

   ![Specificare le informazioni per l'installazione del gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Salvare e conservare la chiave di ripristino in un luogo sicuro. Questa chiave è necessaria se si vuole modificare la posizione, spostare, ripristinare o sostituire un'installazione del gateway.

   Si noti l'opzione **Aggiungi a un cluster di gateway esistente**, che è possibile selezionare quando si installano gateway aggiuntivi per [scenari a disponibilità elevata](#high-availability).

1. Controllare l'area per il servizio cloud del gateway e per il [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/) usato dall'installazione del gateway. Per impostazione predefinita, questa area è la stessa del tenant di Azure AD per l'account Azure.

   ![Confermare l'area per il servizio gateway e il bus di servizio](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Per accettare l'area predefinita, selezionare **Configura**. Tuttavia, se l'area predefinita non è quella più vicina all'utente, è possibile modificarla.

   *Perché modificare l'area per l'installazione del gateway?*

   Ad esempio, per ridurre la latenza, è possibile modificare l'area del gateway nella stessa area dell'app per la logica. In alternativa, è possibile selezionare l'area più vicina all'origine dati locale. La *risorsa per il gateway in Azure* e l'app per la logica possono avere posizioni diverse.

   1. Accanto all'area corrente, selezionare **Cambia area**.

      ![Modificare l'area del gateway attuale](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Nella pagina successiva aprire l'elenco **Seleziona area**, selezionare l'area desiderata e scegliere **Chiudi**.

      ![Selezionare un'altra area per il servizio gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Esaminare le informazioni nella finestra di conferma finale. Questo esempio usa lo stesso account per App per la logica, Power BI, Power Apps e Power Automate, in modo che il gateway sia disponibile per tutti questi servizi. Al termine, selezionare **Chiudi**.

   ![Confermare le informazioni sul gateway dati](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. A questo punto [creare la risorsa di Azure per l'installazione del gateway](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Controllare o modificare le impostazioni di comunicazione

Il gateway dati locale dipende dal [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) per la connettività al cloud e stabilisce le corrispondenti connessioni in uscita all'area di Azure associata del gateway. Se l'ambiente di lavoro richiede l'invio del traffico attraverso un proxy o un firewall per l'accesso a Internet, questa restrizione potrebbe impedire al gateway dati locale di connettersi al servizio cloud del gateway e al bus di servizio di Azure. Il gateway dispone di diverse impostazioni di comunicazione che è possibile modificare. Per altre informazioni, vedere gli argomenti seguenti:

* [Configurazione delle impostazioni di comunicazione per il gateway dati locale](/data-integration/gateway/service-gateway-communication)
* [Configurare le impostazioni proxy per il gateway dati locale](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Supporto per la disponibilità elevata

Per evitare singoli punti di guasto per l'accesso ai dati locali, è possibile avere più installazioni del gateway, solo in modalità standard, ognuna in un computer diverso e impostarle come cluster o gruppo. In questo modo, se il gateway primario non è disponibile, le richieste di dati vengono instradate al secondo gateway e così via. Poiché è possibile installare un solo gateway standard in un computer, è necessario installare ogni gateway aggiuntivo nel cluster in un computer diverso. Tutti i connettori che usano il gateway dati locale ora supportano la disponibilità elevata.

* È necessario disporre già di almeno un'installazione del gateway nell'account Azure come gateway primario e della chiave di ripristino per tale installazione.

* Il gateway primario deve eseguire l'aggiornamento del gateway di novembre 2017 o versione successiva.

Dopo aver configurato il gateway primario, quando si installa un altro gateway, selezionare **Aggiungi a un cluster di gateway esistente**, selezionare il gateway primario, che è il primo gateway installato e specificare la chiave di ripristino per il gateway. Per altre informazioni, vedere [Cluster a disponibilità elevata per il gateway dati locale](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Modificare la posizione, eseguire la migrazione, ripristinare o sostituire un gateway esistente

Per modificare la posizione del gateway, spostare l'installazione del gateway in un nuovo computer, ripristinare un gateway danneggiato o assumere la proprietà di un gateway esistente, è necessaria la chiave di ripristino che è stata specificata durante l'installazione del gateway.

> [!NOTE]
> Prima di ripristinare il gateway nel computer in cui è presente l'installazione del gateway originale, è necessario innanzitutto disinstallare il gateway nel computer. Questa azione disconnette il gateway originale.
> Se si rimuove o si elimina un cluster del gateway per un servizio cloud qualsiasi, non è possibile ripristinare tale cluster.

1. Eseguire il programma di installazione del gateway nel computer in cui è presente il gateway esistente.

1. Quando si apre il programma di installazione accedere con lo stesso account di Azure usato per installare il gateway.

1. Selezionare **Eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente** > **Avanti**, ad esempio:

   ![Selezionare "Eseguire la migrazione, ripristinare o acquisire la proprietà di un gateway esistente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Scegliere tra i cluster e i gateway disponibili e immettere la chiave di ripristino per il gateway selezionato, ad esempio:

   ![Selezionare il gateway e specificare la chiave di ripristino](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Per modificare l'area, selezionare **Cambia area** e quindi scegliere la nuova area.

1. Al termine, selezionare **Configura** in modo che sia possibile completare l'attività.

## <a name="tenant-level-administration"></a>Amministrazione a livello di tenant

Per ottenere la visibilità di tutti i gateway dati locali in un tenant di Azure AD, gli amministratori globali in quel tenant possono accedere all'[interfaccia di amministrazione di Power Platform](https://powerplatform.microsoft.com) come amministratore tenant e selezionare l'opzione **Gateway dati**. Per altre informazioni, vedere [Amministrazione a livello di tenant per il gateway dati locale](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Riavviare il gateway

Per impostazione predefinita, l'installazione del gateway nel computer locale viene eseguita come un account di servizio di Windows denominato "Servizio gateway dati locale". Tuttavia, l'installazione del gateway usa il nome `NT SERVICE\PBIEgwService` per le credenziali dell'account "Accedi come" e dispone delle autorizzazioni di "Accesso come servizio".

> [!NOTE]
> L'account di servizio di Windows è diverso dall'account usato per la connessione alle origini dati locali e dall'account di Azure usato per accedere ai servizi cloud.

Come qualsiasi altro servizio di Windows, è possibile avviare o arrestare il gateway in diversi modi. Per altre informazioni, vedere [Riavviare un gateway dati locale](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Funzionamento del gateway

Gli utenti dell'organizzazione possono accedere ai dati locali per cui hanno già accesso autorizzato. Tuttavia, prima che questi utenti possano connettersi all'origine dati locale, è necessario installare e configurare un gateway dati locale. In genere, un amministratore è la persona che installa e configura il gateway. Queste azioni potrebbero richiedere autorizzazioni di amministratore del server o conoscenze specifiche dei server locali.

Il gateway favorisce comunicazioni in background più veloci e sicure. Questa comunicazione passa da un utente nel cloud, al servizio cloud del gateway e all'origine dati locale. Il servizio cloud gateway crittografa e archivia le credenziali dell'origine dati e i dettagli del gateway. Il servizio instrada anche le query e i relativi risultati tra l'utente, il gateway dati locale e l'origine dati locale.

Il gateway funziona con i firewall e usa solo connessioni in uscita. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Il gateway inoltra i dati dalle origini locali sui canali crittografati tramite il [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md). Il bus di servizio crea un canale tra il gateway e il servizio chiamante, ma non archivia alcun dato. Tutti i dati che attraversano il gateway sono crittografati.

![Architettura del gateway dati locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> In base al servizio cloud, potrebbe essere necessario configurare un'origine dati per il gateway.

I passaggi seguenti descrivono quello che accade quando un utente interagisce con un elemento connesso a un'origine dati locale:

1. Il servizio cloud crea una query con le credenziali crittografate per l'origine dati. Il servizio invia quindi la query e le credenziali alla coda del gateway per l'elaborazione.

1. Il servizio cloud del gateway analizza la query ed esegue il push della richiesta al bus di servizio di Azure.

1. Il bus di servizio di Azure invia le richieste in sospeso al gateway.

1. Il gateway riceve la query, decrittografa le credenziali e si connette a una o più origini dati con tali credenziali.

1. Invia quindi la query all'origine dati per l'esecuzione.

1. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al servizio cloud del gateway. Infine, il servizio cloud del gateway usa i risultati.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticazione nelle origini dati locali

Le credenziali archiviate vengono usate per la connessione dal gateway alle origini dati locali. Indipendentemente dall'utente, il gateway usa le credenziali archiviate per connettersi. Potrebbero esserci eccezioni di autenticazione per servizi specifici, come DirectQuery e LiveConnect per Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

I servizi cloud Microsoft usano [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) per autenticare gli utenti. Un tenant di Azure AD contiene i nomi utente e i gruppi di sicurezza. In genere, l'indirizzo di posta elettronica usato per l'accesso è uguale al nome dell'entità utente (UPN) per l'account.

### <a name="what-is-my-upn"></a>Qual è il mio UPN?

Se non si è amministratore di dominio, è possibile che non si conosca l'UPN. Per trovare l'UPN per l'account, eseguire il comando `whoami /upn` nella workstation. Sebbene appaia come un indirizzo di posta elettronica, il risultato è l'UPN per l'account di dominio locale.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizzare Active Directory locale con Azure AD

L'UPN per gli account Active Directory locali e quello per gli account Azure AD devono essere uguali. Assicurarsi quindi che ogni account Active Directory locale corrisponda all'account Azure AD. I servizi cloud conoscono solo gli account in Azure AD. Non è quindi necessario aggiungere un account ad Active Directory locale. Se l'account non esiste in Azure AD, non è possibile usarlo.

Ecco i modi in cui è possibile far corrispondere gli account Active Directory locali con Azure AD.

* Aggiungere gli account manualmente ad Azure AD.

  Creare un account nel portale di Azure o nell'interfaccia di amministrazione di Microsoft 365. Verificare che il nome dell'account corrisponda all'UPN per l'account Active Directory locale.

* Sincronizzare gli account locali con il tenant di Azure AD usando lo strumento Azure Active Directory Connect.

  Lo strumento Azure AD Connect offre opzioni per la sincronizzazione della directory e la configurazione dell'autenticazione, tra cui la sincronizzazione dell'hash delle password, l'autenticazione pass-through e la federazione. Se non si è amministratore tenant o amministratore del dominio locale, contattare l'amministratore IT per ottenere la configurazione di Azure AD Connect. Azure AD Connect garantisce che l'UPN di Azure AD corrisponda all'UPN di Active Directory locale. Questa corrispondenza consente di usare le connessioni live di Analysis Services con le funzionalità di Power BI o con le funzionalità Single Sign-On (SSO).

  > [!NOTE]
  > La sincronizzazione degli account con lo strumento Azure AD Connect crea nuovi account nel tenant di Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Domande frequenti e risoluzione dei problemi

* [Domande frequenti sul gateway dati locale](/data-integration/gateway/service-gateway-onprem-faq)
* [Risolvere i problemi del gateway dati locale](/data-integration/gateway/service-gateway-tshoot)
* [Monitorare e ottimizzare le prestazioni del gateway](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Passaggi successivi

* [Connessione al gateway dati locale per le app per la logica](../logic-apps/logic-apps-gateway-connection.md)
* [Funzionalità di Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connettori per App per la logica di Azure](../connectors/apis-list.md)
