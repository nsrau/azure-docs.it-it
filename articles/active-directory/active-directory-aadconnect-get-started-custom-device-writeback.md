<properties 
	pageTitle="Abilitazione del writeback dei dispositivi in Azure AD Connect" 
	description="Questo documento descrive come abilitare il writeback dei dispositivi usando Azure AD Connect" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="msStevenPo" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015"
	ms.author="billmath"/>

# Abilitazione del writeback dei dispositivi in Azure AD Connect

Il documento seguente illustra come abilitare la funzionalità di writeback dei dispositivi in Azure AD Connect. Il writeback dei dispositivi viene usato negli scenari seguenti:

Per abilitare l'accesso condizionale in base ai dispositivi alle applicazione protette tramite ADFS 2012 R2 o versioni successive (trust della relying party).

Questo offre maggiore sicurezza e garantisce che l'accesso alle applicazioni venga concesso solo ai dispositivi attendibili. Per altre informazioni sull'accesso condizionale, vedere [Gestione dei rischi con l'accesso condizionale](active-directory-conditional-access.md) e [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.Note]Quando si usano dispositivi registrati nei criteri di accesso condizionale del servizio Registrazione dispositivo di Azure Active Directory, è necessario un abbonamento a Office 365 o una licenza di Azure AD Premium. Questo include i criteri applicati da Active Directory Federation Services (AD FS) alle risorse locali.

## Parte 1: Preparare Azure AD Connect
Per preparare il writeback dei dispositivi, seguire questa procedura.

1.	Dal computer in cui è installato Azure AD, avviare PowerShell con privilegi elevati.

2.	Se il modulo Active Directory PowerShell NON è installato. Installarlo usando il comando seguente:

	Install-WindowsFeature –Name AD-DOMAIN-Services –IncludeManagementTools

3.	Con le credenziali di amministratore dell'organizzazione, eseguire i comandi seguenti e quindi uscire da PowerShell.

	Import-Module 'C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1'

	Initialize-ADSyncDeviceWriteback –DomainName <name> -AdConnectorAccount <account>

Descrizione:



- Se non esistono, crea e configura nuovi contenitori e oggetti in CN=Device Registration Configuration,CN=Services,CN=Configuration,<forest-dn>.



- Se non esistono, crea e configura nuovi contenitori e oggetti in CN=RegisteredDevices,<domain-dn>. Gli oggetti dispositivo verranno creati in questo contenitore.



- Imposta le autorizzazioni necessarie nell'account Azure AD Connector per gestire i dispositivi nella propria istanza di Active Directory.



- Deve essere eseguito in una sola foresta, anche se Azure AD Connect verrà installato in più foreste.

Parametri


- DomainName: dominio di Active Directory in cui verranno creati gli oggetti dispositivo. Nota: tutti i dispositivi per una foresta Active Directory specifica verranno creati in un singolo dominio. 


- AdConnectorAccount: account Active Directory che verrà usato da Azure AD Connect per gestire gli oggetti nella directory.

## Parte 2: Abilitare il writeback dei dispositivi
Per abilitare il writeback dei dispositivi in Azure AD Connect, seguire questa procedura.

1.	Eseguire l'installazione guidata di AAD Connect. Se è la prima volta che si usa la procedura guidata, eseguire un'installazione personalizzata selezionando Personalizza nella schermata Impostazioni rapide ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback1.png)
2.	Se non è la prima volta, selezionare Personalizzazione delle opzioni di sincronizzazione dalla pagina Attività aggiuntive e fare clic su Avanti. ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback2.png)
3.	Nella pagina Funzionalità facoltative l'opzione Writeback dispositivi non sarà più disabilitata. Si noti che se le procedure di preparazione di Azure AD Connect non vengono completate, l'opzione Writeback dispositivi risulterà disabilitata nella pagina Funzionalità facoltative. Selezionare la casella accanto a Writeback dispositivi e fare clic su Avanti. ![Writeback dispositivi](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback3.png)
4.	Nella pagina Writeback il dominio specificato verrà visualizzato come foresta di writeback dei dispositivi predefinita. ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback4.png)
5.	Completare l'installazione guidata senza ulteriori modifiche di configurazione. Se necessario, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).



## Abilitare l'accesso condizionale
Per informazioni dettagliate su come abilitare questo scenario, vedere [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Verificare che i dispositivi siano sincronizzati con Active Directory
Il writeback dei dispositivi dovrebbe funzionare correttamente. Tenere presente che l'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a 3 ore. Per verificare che i dispositivi siano sincronizzati correttamente, al termine dell'esecuzione delle regole di sincronizzazione seguire questa procedura:
 
1.	Avviare il Centro di amministrazione di Active Directory. 
2.	Espandere RegisteredDevices all'interno del dominio che verrà federato. ![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback5.png)
3.	I dispositivi attualmente registrati saranno visualizzati in questo elenco. 

![Installazione personalizzata](./media/active-directory-aadconnect-get-started-custom-device-writeback/devicewriteback6.png)

## Informazioni aggiuntive 


- [Gestione dei rischi con l'accesso condizionale](active-directory-conditional-access.md)
- [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

<!---HONumber=Sept15_HO3-->