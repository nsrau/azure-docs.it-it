---
title: Esercitazione - Configurare LDAPS per Azure Active Directory Domain Services | Microsoft Docs
description: Questa esercitazione illustra come configurare il protocollo sicuro LDAPS (Lightweight Directory Access Protocol) per un dominio gestito di Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: f575dd882c217badb3320b85229149d9793ceb5f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619031"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Esercitazione: Configurare LDAP sicuro per un dominio gestito di Azure Active Directory Domain Services

Per comunicare con il dominio gestito di Azure Active Directory Domain Services (Azure AD DS), viene usato il protocollo LDAP (Lightweight Directory Access Protocol). Per impostazione predefinita, il traffico LDAP non è crittografato, il che rappresenta un problema di sicurezza per molti ambienti. Con Azure AD DS, è possibile configurare il dominio gestito per l'uso del protocollo LDAP sicuro (LDAPS). Quando si usa LDAP sicuro, il traffico viene crittografato. LDAP sicuro è anche noto come LDAP su SSL (Secure Sockets Layer)/TLS (Transport Layer Security).

Questa esercitazione illustra come configurare LDAPS per un dominio gestito di Azure AD DS.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un certificato digitale per l'uso con Azure AD DS
> * Abilitare LDAP sicuro per Azure AD DS
> * Configurare LDAP sicuro per l'uso tramite Internet pubblico
> * Eseguire il binding e il test di LDAP sicuro per un dominio gestito di Azure AD DS

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Lo strumento *LDP.exe* installato nel computer.
    * Se necessario, [installare gli Strumenti di amministrazione remota del server][rsat] per *Active Directory Domain Services e LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione si configura LDAP sicuro per il dominio gestito di Azure AD DS usando il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Creare un certificato per LDAP sicuro

Per LDAP sicuro, viene usato un certificato digitale per crittografare la comunicazione. Questo certificato digitale viene applicato al dominio gestito di Azure AD DS e consente a strumenti come *LDP.exe* di usare una comunicazione crittografata sicura durante l'esecuzione di query sui dati. Esistono due modi per creare un certificato per l'accesso LDAP sicuro al dominio gestito:

* Un certificato di un'autorità di certificazione (CA) pubblica o globale (enterprise).
    * Se l'organizzazione ottiene i certificati da un'autorità di certificazione pubblica, ottenere il certificato LDAP sicuro dalla stessa autorità. Se si usa un'autorità di certificazione globale (enterprise), ottenere il certificato LDAP sicuro dalla stessa autorità.
    * Una CA pubblica è adatta solo se si usa un nome DNS personalizzato con il dominio gestito di Azure AD DS. Se il nome di dominio DNS del dominio gestito termina con *.onmicrosoft.com*, non è possibile creare un certificato digitale per proteggere la connessione con tale dominio predefinito. Microsoft è proprietaria del dominio *.onmicrosoft.com*, quindi una CA pubblica non emetterà un certificato. In questo scenario, creare un certificato autofirmato e usarlo per configurare l'accesso LDAP sicuro.
* Un certificato autofirmato creato dall'utente.
    * Questo approccio è valido a scopo di test ed è illustrato in questa esercitazione.

Il certificato richiesto o creato deve soddisfare i requisiti seguenti. Il dominio gestito riscontra problemi se si abilita LDAP sicuro con un certificato non valido:

* **Autorità di certificazione attendibile** : il certificato deve essere emesso da un'autorità ritenuta attendibile dai computer connessi al dominio gestito tramite accesso LDAP sicuro. Questa autorità può essere un'autorità di certificazione pubblica o globale (enterprise) considerata attendibile da questi computer.
* **Durata** : il certificato deve essere valido almeno per i 3-6 mesi successivi. L'accesso LDAP sicuro al dominio gestito viene interrotto allo scadere del certificato.
* **Nome soggetto** : il nome del soggetto nel certificato deve includere il dominio gestito. Ad esempio, se il dominio è denominato *contoso.com*, il nome del soggetto nel certificato deve essere *contoso.com*.
    * Il nome DNS o il nome alternativo del soggetto del certificato deve essere un certificato con caratteri jolly per garantire che il protocollo LDAP sicuro funzioni correttamente con Azure AD Domain Services. I controller di dominio usano nomi casuali e possono essere rimossi o aggiunti per assicurarsi che il servizio rimanga disponibile.
* **Utilizzo di chiavi**: il certificato deve essere configurato per le *firme digitali* e la *crittografia a chiave*.
* **Scopo del certificato** : il certificato deve essere valido per l'autenticazione server SSL.

In questa esercitazione verrà creato un certificato autofirmato per LDAP sicuro tramite PowerShell. Aprire una finestra di PowerShell come **amministratore** e immettere i comandi seguenti. Sostituire la variabile *$dnsName* con il nome DNS usato dal dominio gestito, ad esempio *contoso.com*:

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="contoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject $dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

L'output di esempio seguente mostra che il certificato è stato generato correttamente ed è archiviato nell'archivio certificati locale (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject $dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=contoso.com
```

## <a name="understand-and-export-required-certificates"></a>Identificare ed esportare i certificati richiesti

Per usare LDAP sicuro, il traffico di rete viene crittografato con l'infrastruttura a chiave pubblica (PKI).

* Al dominio gestito di Azure AD DS viene applicata una chiave **privata**.
    * Questa chiave privata viene usata per *decrittografare* il traffico LDAP sicuro. La chiave privata deve essere applicata solo al dominio gestito di Azure AD DS e non distribuita su larga scala ai computer client.
    * Un certificato che include la chiave privata usa il formato di file *PFX*.
* Ai computer client viene applicata una chiave **pubblica**.
    * Questa chiave pubblica viene usata per *crittografare* il traffico LDAP sicuro. La chiave pubblica può essere distribuita ai computer client.
    * I certificati senza la chiave privata usano il formato di file *CER*.

Queste due chiavi, *pubblica* e *privata*, assicurano che solo i computer appropriati possano comunicare correttamente tra loro. Se si usa una CA pubblica o una CA globale (enterprise), viene emesso un certificato che include la chiave privata e che può essere applicato a un dominio gestito di Azure AD DS. La chiave pubblica deve essere già nota e considerata attendibile dai computer client. In questa esercitazione è stato creato un certificato autofirmato con la chiave privata, quindi è necessario esportare i componenti pubblici e privati appropriati.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Esportare un certificato per Azure AD DS

Per poter usare il certificato digitale creato nel passaggio precedente con il dominio gestito di Azure AD DS, è necessario prima esportare il certificato in un file *PFX* che include la chiave privata.

1. Per aprire la finestra di dialogo *Esegui*, premere il tasto **WINDOWS** e **R**.
1. Aprire Microsoft Management Console (MMC) immettendo **mmc** nella finestra di dialogo *Esegui* e quindi fare clic su **OK**.
1. Alla richiesta di **Controllo dell'account utente** fare clic su **Sì** per avviare MMC come amministratore.
1. Nel menu **File** fare clic su **Aggiungi/Rimuovi snap-in**.
1. Nella procedura guidata **Snap-in certificati** scegliere **Account del computer** e quindi selezionare **Avanti**.
1. Nella pagina **Seleziona computer** scegliere **Computer locale (il computer su cui è in esecuzione questa console)** e quindi selezionare **Fine**.
1. Nella finestra di dialogo **Aggiungi o rimuovi snap-in** fare clic su **OK** per aggiungere lo snap-in Certificati a MMC.
1. Nella finestra di MMC espandere **Radice console**. Selezionare **Certificati (computer locale)** , quindi espandere il nodo **Personale**, poi il nodo **Certificati**.

    ![Aprire l'archivio certificati personale in Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Viene visualizzato il certificato autofirmato creato nel passaggio precedente, ad esempio *contoso.com*. Fare clic con il pulsante destro del mouse su questo certificato, quindi scegliere **Tutte le attività > Esporta**.

    ![Esportare il certificato in Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. Nella **Esportazione guidata certificati** fare clic su **Avanti**.
1. È necessario esportare la chiave privata per il certificato. Se la chiave privata non è inclusa nel certificato esportato, l'azione per abilitare LDAP sicuro per il dominio gestito ha esito negativo.

    Nella pagina **Esportazione della chiave privata con il certificato** scegliere **Sì, esporta la chiave privata** e quindi fare clic su **Avanti**.
1. I domini gestiti di Azure AD DS supportano solo il formato di file di certificato *PFX* che include la chiave privata. Non esportare il certificato come formato di file *CER* senza la chiave privata.

    Nella pagina **Formato file di esportazione** selezionare **Scambio di informazioni personali - PKCS #12 (.PFX)** come formato di file per il certificato esportato. Selezionare la casella accanto a *Se possibile, includi tutti i certificati nel percorso certificazione*:

    ![Scegliere l'opzione per esportare il certificato nel formato di file PKCS 12 (PFX)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Poiché questo certificato viene usato per decrittografare i dati, è consigliabile controllare con attenzione l'accesso. È possibile usare una password per proteggere l'uso del certificato. Senza la password corretta, il certificato non può essere applicato a un servizio.

    Nella pagina **Sicurezza** scegliere l'opzione **Password** per proteggere il file di certificato *PFX*. Immettere e confermare una password, quindi selezionare **Avanti**. Questa password viene usata nella sezione successiva per abilitare LDAP sicuro per il dominio gestito di Azure AD DS.
1. Nella pagina **File da esportare** specificare il nome del file e il percorso in cui esportare il certificato, ad esempio *C:\Users\accountname\azure-ad-ds.pfx*.
1. Nella pagina di verifica selezionare **Fine** per esportare il certificato in un file *PFX*. Quando il certificato viene esportato, viene visualizzata una finestra di dialogo di conferma.
1. Lasciare aperta la console MMC per l'uso nella sezione seguente.

### <a name="export-a-certificate-for-client-computers"></a>Esportare un certificato per i computer client

I computer client devono considerare attendibile l'autorità emittente del certificato LDAP sicuro per stabilire una connessione con il dominio gestito tramite LDAPS. I computer client necessitano di un certificato per crittografare correttamente i dati decrittografati da Azure AD DS. Se si usa una CA pubblica, il computer deve considerare automaticamente attendibili tali autorità di certificazione e avere un certificato corrispondente. In questa esercitazione viene usato un certificato autofirmato e viene generato un certificato che include la chiave privata del passaggio precedente. A questo punto esportare e quindi installare il certificato autofirmato nell'archivio certificati attendibili del computer client:

1. Tornare nella console MMC per l'archivio *Certificati (computer locale) > Personale > Certificati*. Viene visualizzato il certificato autofirmato creato in un passaggio precedente, ad esempio *contoso.com.* Fare clic con il pulsante destro del mouse su questo certificato, quindi scegliere **Tutte le attività > Esporta**.
1. Nella **Esportazione guidata certificati** fare clic su **Avanti**.
1. Poiché per i client la chiave privata non è necessaria, nella pagina **Esporta chiave privata** scegliere **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
1. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** come formato di file per il certificato esportato:

    ![Scegliere l'opzione per esportare il certificato nel formato di file codificato Base-64 X.509 (.CER)](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Nella pagina **File da esportare** specificare il nome del file e il percorso in cui esportare il certificato, ad esempio *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Nella pagina di verifica selezionare **Fine** per esportare il certificato in un file *CER*. Quando il certificato viene esportato, viene visualizzata una finestra di dialogo di conferma.

Il file di certificato *CER* può ora essere distribuito ai computer client che devono considerare attendibile la connessione LDAP sicura al dominio gestito di Azure AD DS. Installare il certificato nel computer locale.

1. Aprire Esplora file e passare al percorso in cui è stato salvato il file di certificato *CER*, ad esempio*C:\Users\accountname\azure-ad-ds-client.cer*.
1. Fare clic con il pulsante destro del mouse sul file di certificato *CER*, quindi scegliere **Installa certificato**.
1. Nella **Importazione guidata certificati** scegliere di archiviare il certificato nel *Computer locale*, quindi fare clic su **Avanti**:

    ![Scegliere l'opzione per importare il certificato nell'archivio del computer locale](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Quando richiesto, scegliere **Sì** per consentire al computer di apportare modifiche.
1. Scegliere **Seleziona automaticamente l'archivio certificati secondo il tipo di certificato**, quindi fare clic su **Avanti**.
1. Nella pagina di verifica selezionare **Fine** per importare il certificato *CER*. Quando il certificato viene importato, viene visualizzata una finestra di dialogo di conferma.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Abilitare LDAP sicuro per Azure AD DS

Con un certificato digitale creato ed esportato che include la chiave privata e il computer client impostato in modo da considerare attendibile la connessione, è ora possibile abilitare LDAP sicuro nel dominio gestito di Azure AD DS. Per abilitare LDAP sicuro in un dominio gestito di Azure AD DS, seguire questa procedura di configurazione:

1. Nel [portale di Azure](https://portal.azure.com) cercare *servizi di dominio* nella casella **Cerca risorse**. Selezionare **Azure AD Domain Services** dai risultati della ricerca.

    ![Cercare e selezionare il dominio gestito di Azure AD DS nel portale di Azure](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Scegliere il dominio gestito, ad esempio *contoso.com*.
1. Sul lato sinistro della finestra di Azure AD DS scegliere **LDAP sicuro.**
1. Per impostazione predefinita, l'accesso LDAP sicuro al dominio gestito è disabilitato. Impostare **LDAP sicuro** su **Abilita**.
1. Per impostazione predefinita, l'accesso LDAP sicuro al dominio gestito su Internet è disabilitato. Quando si abilita l'accesso LDAP sicuro tramite Internet, il dominio è soggetto ad attacchi di forza bruta sulle password tramite Internet. Nel passaggio successivo, viene configurato un gruppo di sicurezza di rete per bloccare l'accesso consentendolo solo agli intervalli di indirizzi IP di origine necessari.

    Impostare **Abilita accesso LDAP sicuro tramite Internet** su **Abilita**.

1. Selezionare l'icona della cartella accanto a **File PFX con certificato LDAP sicuro**. Individuare il percorso del file *PFX*, quindi selezionare il certificato che include la chiave privata, creato in un passaggio precedente.

    Come indicato nella sezione precedente sui requisiti dei certificati, non è possibile usare un certificato di una CA pubblica con il dominio predefinito *.onmicrosoft.com*. Microsoft è proprietaria del dominio *.onmicrosoft.com*, quindi una CA pubblica non emetterà un certificato. Assicurarsi che il certificato sia nel formato appropriato. In caso contrario, la piattaforma Azure genera errori di convalida del certificato quando si abilita LDAP sicuro.

1. Immettere la **Password per decrittografare il file PFX** impostata in un passaggio precedente quando il certificato è stato esportato in un file *PFX*.
1. Selezionare **Salva** per abilitare LDAP sicuro.

    ![Abilitare LDAP sicuro per il dominio gestito di Azure AD DS nel portale di Azure](./media/tutorial-configure-ldaps/enable-ldaps.png)

Viene visualizzata una notifica che informa che è in corso la configurazione di LDAP sicuro per il dominio gestito. Finché questa operazione non viene completata, non è possibile modificare altre impostazioni per il dominio gestito.

Per abilitare LDAP sicuro per il dominio gestito sono necessari alcuni minuti. Se il certificato LDAP sicuro fornito non corrisponde ai criteri richiesti, l'azione per abilitare LDAP sicuro per il dominio gestito ha esito negativo. In genere l'errore si verifica se il nome di dominio non è corretto oppure se il certificato sta per scadere o è già scaduto. È possibile creare di nuovo il certificato con parametri validi, quindi abilitare LDAP sicuro usando questo certificato aggiornato.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Bloccare l'accesso LDAP sicuro su Internet

Quando si abilita l'accesso LDAP sicuro al dominio gestito di Azure AD DS tramite Internet, viene creata una minaccia per la sicurezza. Il dominio gestito è raggiungibile da Internet sulla porta TCP 636. È consigliabile limitare l'accesso al dominio gestito a specifici indirizzi IP noti per l'ambiente. È possibile usare una regola del gruppo di sicurezza di rete di Azure per limitare l'accesso a LDAP sicuro.

Creare una regola per consentire l'accesso LDAP sicuro in ingresso sulla porta TCP 636 da un set specificato di indirizzi IP. Una regola *DenyAll* predefinita con una priorità più bassa si applica a tutto il traffico in ingresso proveniente da Internet, quindi solo gli indirizzi specificati possono raggiungere il dominio gestito di Azure AD DS tramite il protocollo LDAP sicuro.

1. Nel riquadro di spostamento sinistro del portale di Azure selezionare *Gruppi di risorse*.
1. Scegliere il gruppo di risorse, ad esempio *myResourceGroup*, quindi selezionare il gruppo di sicurezza di rete, ad esempio *AADDS-contoso.com-NSG*.
1. Viene visualizzato l'elenco delle regole di sicurezza in ingresso e in uscita esistenti. Sul lato sinistro della finestra del gruppo di sicurezza di rete scegliere **Sicurezza > Regole di sicurezza in ingresso**.
1. Selezionare **Aggiungi**, quindi creare una regola per consentire la porta *TCP* *636*. Per una maggiore sicurezza, scegliere l'origine come *Indirizzi IP* e quindi specificare il proprio indirizzo IP o l'intervallo valido per l'organizzazione.

    | Impostazione                           | Valore        |
    |-----------------------------------|--------------|
    | Source (Sorgente)                            | Indirizzi IP |
    | Indirizzi IP/intervalli CIDR di origine | Un indirizzo IP o un intervallo valido per l'ambiente |
    | Intervalli di porte di origine                | *            |
    | Destination                       | Qualsiasi          |
    | Intervalli di porte di destinazione           | 636          |
    | Protocollo                          | TCP          |
    | Azione                            | CONSENTI        |
    | Priorità                          | 401          |
    | NOME                              | AllowLDAPS   |

1. Quando si è pronti, selezionare **Aggiungi** per salvare e applicare la regola.

    ![Creare una regola del gruppo di sicurezza di rete per proteggere l'accesso LDAPS su Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Configurare la zona DNS per l'accesso esterno

Dopo aver abilitato l'accesso LDAP sicuro tramite Internet, aggiornare la zona DNS in modo che i computer client possano trovare il dominio gestito. L'*Indirizzo IP esterno per LDAP sicuro*  è riportato nella scheda **Proprietà** del dominio gestito di Azure AD DS:

![Visualizzare l'indirizzo IP esterno per LDAP sicuro per il dominio gestito di Azure AD DS nel portale di Azure](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configurare il provider DNS esterno per creare un record host, ad esempio *ldaps*, da risolvere in questo indirizzo IP esterno. Per eseguire prima il test in locale nel computer, è possibile creare una voce nel file hosts di Windows. Per modificare correttamente il file hosts nel computer locale, aprire il *Blocco note* come amministratore, quindi aprire il file *C:\Windows\System32\drivers\etc*

La voce DNS di esempio seguente, con il provider DNS esterno o nel file hosts locale, risolve il traffico per *ldaps.contoso.com* nell'indirizzo IP esterno *40.121.19.239*:

```
40.121.19.239    ldaps.contoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testare le query sul dominio gestito

Per eseguire la connessione e il binding al dominio gestito di Azure AD DS e svolgere ricerche tramite LDAP, usare *LDP.exe*. Questo strumento è incluso nel pacchetto di Strumenti di amministrazione remota del server. Per altre informazioni, vedere [Installare Strumenti di amministrazione remota del server][rsat].

1. Aprire *LDP.exe* e connettersi al dominio gestito. Selezionare **Connessione **e quindi scegliere** Connetti**.
1. Immettere il nome di dominio DNS LDAP sicuro del dominio gestito creato nel passaggio precedente, ad esempio *ldaps.contoso.com.* Per usare LDAP sicuro, impostare **Porta** su *636*, quindi selezionare la casella **SSL**.
1. Fare clic su **OK** per connettersi al dominio gestito.

Eseguire quindi il binding al dominio gestito di Azure AD DS. Gli utenti e gli account del servizio non possono eseguire binding LDAP semplici se la sincronizzazione degli hash delle password NTLM è stata disabilitata nell'istanza di Azure AD DS. Per altre informazioni sulla disabilitazione della sincronizzazione degli hash delle password NTLM, vedere [Proteggere il dominio gestito di Azure AD DS][secure-domain].

1. Selezionare l'opzione di menu **Connessione**, quindi scegliere **Associa**.
1. Fornire le credenziali di un account utente appartenente al gruppo *Amministratori di AAD DC*, ad esempio *contosoadmin*. Immettere la password dell'account utente, quindi immettere il dominio, ad esempio *contoso.com*.
1. Per **Tipo di binding** scegliere l'opzione *Binding con credenziali*.
1. Selezionare **OK** per eseguire il binding al dominio gestito di Azure AD DS.

Per visualizzare gli oggetti archiviati nel dominio gestito di Azure AD DS:

1. Scegliere l'opzione di menu **Visualizza** e quindi scegliere **Albero**.
1. Lasciare vuoto il campo *BaseDN*, quindi fare clic su **OK**.
1. Scegliere un contenitore, ad esempio *AADDC Users*, quindi fare clic con il pulsante destro del mouse sul contenitore e scegliere **Cerca**.
1. Lasciare i campi prepopolati impostati, quindi selezionare **Esegui**. I risultati della query vengono visualizzati nella finestra a destra.

    ![Cercare gli oggetti nel dominio gestito di Azure AD DS tramite LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Per eseguire direttamente una query su un contenitore specifico, nel menu **Visualizza > Albero** è possibile specificare un oggetto **BaseDN**, ad esempio*OU=AADDC Users,DC=CONTOSO,DC=COM* o *OU=AADDC Computers,DC=CONTOSO,DC=COM*. Per altre informazioni su come formattare e creare query, vedere [Nozioni di base sulle query LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Pulire le risorse

Se è stata aggiunta una voce DNS al file hosts locale del computer per testare la connettività per questa esercitazione, rimuovere questa voce e aggiungere un record formale nella zona DNS. Per rimuovere la voce dal file hosts locale, completare i passaggi seguenti:

1. Nel computer locale aprire il *Blocco note* come amministratore
1. Individuare e aprire il file *C:\Windows\System32\drivers\etc*
1. Eliminare la riga per il record aggiunto, ad esempio `40.121.19.239    ldaps.contoso.com`

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un certificato digitale per l'uso con Azure AD DS
> * Abilitare LDAP sicuro per Azure AD DS
> * Configurare LDAP sicuro per l'uso tramite Internet pubblico
> * Eseguire il binding e il test di LDAP sicuro per un dominio gestito di Azure AD DS

> [!div class="nextstepaction"]
> [Informazioni su come funziona la sincronizzazione in un dominio gestito di Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
