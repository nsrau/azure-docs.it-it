<properties
	pageTitle="Azure AD Connect: Abilitazione del writeback dei dispositivi | Microsoft Azure"
	description="Questo documento descrive come abilitare il writeback dei dispositivi usando Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/12/2015"
	ms.author="billmath;andkjell"/>

# Abilitazione del writeback dei dispositivi in Azure AD Connect

Il documento seguente illustra come abilitare la funzionalità di writeback dei dispositivi in Azure AD Connect. Il writeback dei dispositivi viene usato negli scenari seguenti:

Per abilitare l'accesso condizionale in base ai dispositivi alle applicazione protette tramite ADFS 2012 R2 o versioni successive (trust della relying party).

Questo offre maggiore sicurezza e garantisce che l'accesso alle applicazioni venga concesso solo ai dispositivi attendibili. Per altre informazioni sull'accesso condizionale, vedere [Gestione dei rischi con l'accesso condizionale](active-directory-conditional-access.md) e [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.NOTE]Una sottoscrizione di Azure AD Premium è necessaria per il writeback dei dispositivi.


## Parte 1: Installare Azure AD Connect
1. Installare Azure AD Connect usando le impostazioni personalizzate o rapide Si consiglia di sincronizzare utenti e gruppi prima di abilitare il writeback dei dispositivi.

## Parte 2: Preparare Active Directory
Per preparare il writeback dei dispositivi, seguire questa procedura.

1.	Dal computer in cui è installato Azure AD, avviare PowerShell con privilegi elevati.

2.	Se il modulo Active Directory PowerShell NON è installato. Installarlo usando il comando seguente:

	`Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools`

3.	Con le credenziali di amministratore dell'organizzazione, eseguire i comandi seguenti e quindi uscire da PowerShell.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`


![PowerShell](./media/active-directory-aadconnect-get-started-custom-device-writeback/powershell.png)

Descrizione:

- Se non esistono, crea e configura nuovi contenitori e oggetti in CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
- Se non esistono, crea e configura nuovi contenitori e oggetti in CN=RegisteredDevices,[domain-dn]. Gli oggetti dispositivo verranno creati in questo contenitore.
- Imposta le autorizzazioni necessarie nell'account Azure AD Connector per gestire i dispositivi nella propria istanza di Active Directory.
- Deve essere eseguito in una sola foresta, anche se Azure AD Connect verrà installato in più foreste.

Parametri

- DomainName: dominio di Active Directory in cui verranno creati gli oggetti dispositivo. Nota: tutti i dispositivi per una foresta Active Directory specifica verranno creati in un singolo dominio.
- AdConnectorAccount: account Active Directory che verrà usato da Azure AD Connect per gestire gli oggetti nella directory. Si tratta dell'account usato dalla sincronizzazione di Azure AD Connect per connettersi ad Active Directory. Se è stata eseguita l'installazione usando le impostazioni rapide, è l'account con prefisso MSOL\_.

## Parte 3: Abilitare il writeback dei dispositivi in Azure AD Connect
Per abilitare il writeback dei dispositivi in Azure AD Connect, seguire questa procedura.

1.	Eseguire nuovamente l'installazione guidata. Selezionare **Personalizzazione delle opzioni di sincronizzazione** dalla pagina Attività aggiuntive e fare clic su **Avanti**. ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
2.	Nella pagina Funzionalità facoltative l'opzione Writeback dispositivi non sarà più disabilitata. Si noti che se le procedure di preparazione di Azure AD Connect non vengono completate, l'opzione Writeback dispositivi risulterà disabilitata nella pagina Funzionalità facoltative. Selezionare la casella per il writeback dei dispositivi e fare clic su **Avanti**. Se la casella di controllo è ancora disabilitata, vedere la sezione sulla [risoluzione dei problemi](#the-writeback-checkbox-is-still-disabled). ![Writeback dispositivi](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
3.	Nella pagina Writeback il dominio specificato verrà visualizzato come Foresta writeback dispositivi predefinita. ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
4.	Completare l'installazione guidata senza ulteriori modifiche di configurazione. Se necessario, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).


## Abilitare l'accesso condizionale
Per informazioni dettagliate su come abilitare questo scenario, vedere [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Verificare che i dispositivi siano sincronizzati con Active Directory
Il writeback dei dispositivi dovrebbe funzionare correttamente. Tenere presente che l'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a 3 ore. Per verificare che i dispositivi siano sincronizzati correttamente, al termine dell'esecuzione delle regole di sincronizzazione seguire questa procedura:

1.	Avviare il Centro di amministrazione di Active Directory.
2.	Espandere RegisteredDevices all'interno del dominio che verrà federato. ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	I dispositivi attualmente registrati saranno visualizzati in questo elenco.

![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Risoluzione dei problemi

### La casella di controllo del writeback è ancora disabilitata
Se la casella di controllo per il writeback dei dispositivi non è ancora abilitata anche se sono stati seguiti i passaggi precedenti, la procedura seguente consentirà di verificare le caratteristiche dell'installazione guidata prima dell'abilitazione della casella.

Attività iniziali:

- Assicurarsi che almeno una foresta disponga di Windows Server 2012 R2. Il tipo di oggetto del dispositivo deve essere presente.
- Se l'installazione guidata è già in esecuzione, non verranno rilevate tutte le modifiche. In questo caso, completare l'installazione guidata ed eseguirla nuovamente.
- Assicurarsi che l'account specificato nello script di inizializzazione sia effettivamente l'utente corretto usato da Active Directory Connector. A questo scopo, seguire questa procedura:
	- Avviare **Servizio di sincronizzazione** dal menu Start.
	- Aprire la scheda **Connettori**.
	- Trovare il connettore con il tipo Servizi di dominio di Active Directory e selezionarlo.
	- In **Azioni** selezionare **Proprietà**.
	- Passare a **Connetti a Foresta Active Directory**. Verificare che il dominio e il nome specificati in questa schermata corrispondano all'account specificato per lo script. ![Account connettore](./media/active-directory-aadconnect-get-started-custom-device-writeback/connectoraccount.png)

Verificare la configurazione in Active Directory: - Verificare che l'oggetto Device Registration Service si trovi nel percorso seguente (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) nel contesto dei nomi di configurazione.

![Risoluzione dei problemi1](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot1.png)

- Verificare che sia presente un solo oggetto di configurazione cercando lo spazio dei nomi di configurazione. Se sono presenti più oggetti, eliminare il duplicato.

![Risoluzione dei problemi2](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot2.png)

- Assicurarsi che nell'oggetto Device Registration Service sia presente l'attributo msDS-DeviceLocation con un valore associato. Cercare la posizione e assicurarsi che sia presente con il tipo di oggetto msDS-DeviceContainer.

![Risoluzione dei problemi3](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot3.png)

![Risoluzione dei problemi4](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot4.png)

- Verificare che l'account usato da Active Directory Connector disponga delle autorizzazioni necessarie per il contenitore Dispositivi registrati trovato nel passaggio precedente. Si tratta delle autorizzazioni previste per questo contenitore:

![Risoluzione dei problemi5](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot5.png)

- Verificare che l'account Active Directory disponga delle autorizzazioni nell'oggetto CN=Device Registration Configuration,CN=Services,CN=Configuration.

![Risoluzione dei problemi6](./media/active-directory-aadconnect-get-started-custom-device-writeback/troubleshoot6.png)

## Informazioni aggiuntive
- [Gestione dei rischi con l'accesso condizionale](active-directory-conditional-access.md)
- [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->