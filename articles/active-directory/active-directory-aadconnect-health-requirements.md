<properties 
	pageTitle="Requisiti di Azure AD Connect Health" 
	description="Si tratta della pagina di Azure AD Connect Health che descrive i requisiti e l'installazione dell'agente." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>


<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>


# Requisiti di Azure Active Directory Connect Health
La documentazione seguente è un elenco di requisiti che devono essere soddisfatti per poter procedere con Azure AD Connect Health.



## Licenza Azure AD Premium

Azure AD Connect Health è una funzionalità di Azure AD Premium e richiede una licenza Azure AD Premium. Per ottenere una licenza, vedere Introduzione a Azure AD Premium.
 

## È necessario essere un amministratore globale del tenant Azure AD.

Per impostazione predefinita, gli amministratori globali possono accedere alle informazioni fornite da Azure AD Connect Health. Se non si è un amministratore globale del tenant Azure AD federato con Active Directory locale, non sarà possibile creare un'istanza del servizio Azure AD Connect Health. Assicurarsi di essere un amministratore globale. Per altre informazioni, vedere [Amministrazione della directory di Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx).
 

## Agente di Azure AD Connect Health installato in ogni server di destinazione

Per Azure AD Connect Health è necessario che nei server di destinazione sia installato un agente, che fornirà i dati visualizzati nel portale di Azure AD Connect Health. Questo significa che, per poter ottenere i dati nell'infrastruttura locale ADFS, l'agente deve essere installato nei server ADFS, inclusi i server proxy ADFS e i server proxy applicazione Web. Per informazioni sull'installazione dell'agente di Azure AD Connect Health, vedere i passaggi di installazione dell'agente di Azure AD Connect Health.


## Requisiti dell'agente di Azure AD Connect Health

Nelle sezioni seguenti vengono descritti i requisiti specifici per l'agente di Azure AD Connect Health.
 

### Download dell’agente di Azure AD Connect Health

Per una guida introduttiva su Azure AD Connect Health, è possibile scaricare la versione più recente dell’agente da qui: [Download dell’agente di Azure AD Connect Health.](http://go.microsoft.com/fwlink/?LinkID=518973) Assicurarsi di aver aggiunto il servizio da Marketplace prima di installare gli agenti.

 
### Connettività in uscita agli endpoint di servizio di Azure
Durante l'installazione e la fase di esecuzione, l'agente richiede la connettività agli endpoint di servizio di Azure AD Connect Health elencati sotto. Se si blocca la connettività in uscita, assicurarsi che gli endpoint seguenti vengano aggiunti all'elenco di quelli consentiti:

- **.servicebus.windows.net - Porta: 5671
- https://*.adhybridhealth.azure.com/
- https://*.table.core.windows.net/
- https://policykeyservice.dc.ad.msft.net/
- https://login.windows.net
- https://login.microsoftonline.com
- https://secure.aadcdn.microsoftonline-p.com 

## Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE
I siti Web seguenti devono essere consentiti se la funzionalità Protezione avanzata di IE è abilitata nel server in cui verrà installato l'agente.

- https://login.microsoftonline.com 
- https://secure.aadcdn.microsoftonline-p.com
- https://login.windows.net
- Server federativo per l'organizzazione ritenuto attendibile da Azure Active Directory, ad esempio: https://sts.contoso.com 


### Per ADFS, è necessario che sia attivato il controllo di ADFS perché sia possibile usare la funzionalità di analisi dei dati di utilizzo

Per poter usare questa funzionalità per raccogliere dati e analizzarli, l'agente di Azure AD Connect Health deve poter accedere alle informazioni presenti nei log di controllo di ADFS. Questi log non sono abilitati per impostazione predefinita. Questo requisito si applica solo ai server federativi ADFS. Non è necessario abilitare il controllo nei server proxy ADFS o nei server proxy applicazione Web. Usare le procedure seguenti per abilitare il controllo di ADFS e individuare i log di controllo di ADFS.

#### Per abilitare il controllo per ADFS 2.0

1. Fare clic su **Avvia**, scegliere **Programmi**, scegliere **Strumenti di amministrazione** e quindi fare clic su **Criteri di sicurezza locali**.
1. Passare alla cartella **Impostazioni sicurezza\\Criteri locali\\Gestione diritti utente** e quindi fare doppio clic su Generazione di controlli di sicurezza.
1. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio ADFS 2.0. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
1. Aprire il prompt dei comandi con privilegi elevati ed eseguire il comando seguente per abilitare il controllo: `auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable`
1. Chiudere Criteri di sicurezza locali e quindi aprire lo snap-in Gestione. Per aprire lo snap-in Gestione, fare clic su **Start**, scegliere **Programmi**, scegliere **Strumenti di amministrazione** e quindi fare clic su Gestione ADFS 2.0.
1. Nel riquadro Azioni fare clic su Modifica proprietà servizio federativo.
1. Nella finestra di dialogo **Proprietà servizio federativo** fare clic sulla scheda **Eventi**.
1. Selezionare le caselle di controllo **Operazioni riuscite** e **Operazioni non riuscite**.
1. Fare clic su **OK**.

#### Per abilitare il controllo per ADFS in Windows Server 2012 R2

1. Aprire **Criteri di sicurezza locali** aprendo **Server Manager** nella schermata Start oppure Server Manager nella barra delle applicazioni sul desktop, quindi fare clic su **Strumenti/Criteri di sicurezza locali**.
1. Passare alla cartella **Impostazioni sicurezza\\Criteri locali\\Assegnazione diritti utente** e quindi fare doppio clic su **Generazione di controlli di sicurezza**.
1. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio ADFS. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
1. Aprire il prompt dei comandi con privilegi elevati ed eseguire il comando seguente per abilitare il controllo: `auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.`
1. Chiudere **Criteri di sicurezza locali** e quindi aprire lo snap-in **Gestione ADFS**. In Server Manager fare clic su Strumenti e quindi selezionare Gestione ADFS.
1. Nel riquadro Azioni fare clic su **Modifica proprietà servizio federativo**.
1. Nella finestra di dialogo Proprietà servizio federativo fare clic sulla scheda **Eventi**.
1. Selezionare le caselle di controllo **Operazioni riuscite e Operazioni non riuscite** e quindi fare clic su **OK**.






#### Per individuare i log di controllo di ADFS


1. Aprire il **Visualizzatore eventi**.</li>
1. Passare a Registri di Windows e selezionare **Sicurezza**.
1. A destra fare clic su **Filtro registro corrente**.
1. In Origine evento selezionare **Controllo di ADFS**.

![Log di controllo di ADFS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Se sono presenti criteri di gruppo che disabilitano il controllo ADFS, l'agente di Azure AD Connect Health non sarà in grado di raccogliere informazioni. Assicurarsi che non siano presenti criteri di gruppo che potrebbero disabilitare il controllo.


### Installazione dell'agente in server Windows Server 2008 R2

Per i server Windows Server 2008 R2, eseguire le operazioni seguenti:

1. Assicurarsi che il server venga eseguito con il Service Pack 1 o versione successiva.
1. Disattivare la funzionalità Protezione avanzata di IE per l'installazione dell'agente:
1. Installare Windows PowerShell 4.0 in ognuno dei server prima di installare l'agente per l'integrità AD. Per installare Windows PowerShell 4.0:
 - Installare [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) usando il collegamento seguente per scaricare il programma di installazione offline.
 - Installare PowerShell ISE (da Funzionalità Windows)
 - Installare [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installare Internet Explorer versione 10 o successiva nel server. Questa operazione è necessaria perché il Servizio integrità possa eseguire l'autenticazione usando le credenziali di amministratore di Azure.
1. Per altre informazioni sull'installazione di Windows PowerShell 4.0 in Windows Server 2008 R2, vedere l'articolo wiki [qui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

## Distribuzione dell'agente di Azure AD Connect Health
Questa sezione illustra le procedure per installare e configurare l'agente di Azure AD Connect Health nei server. Tenere presente che, prima di visualizzare i dati nell'istanza di Azure AD Connect Health, è necessario installare l'agente di Azure AD Connect Health nei server di destinazione. Assicurarsi di completare i requisiti elencati sopra prima di installare l'agente. Per scaricare l'agente, fare clic sul collegamento sopra e quindi seguire i passaggi seguenti.


Fare doppio clic sul file EXE scaricato. Nella prima schermata fare clic su Installa.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Una volta completata l'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Verrà avviato un prompt dei comandi seguito da codice PowerShell che eseguirà Register-AzureADConnectHealthADFSAgent. Verrà visualizzata la richiesta di accedere ad Azure. Procedere all'esecuzione dell'accesso.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Dopo l'accesso, PowerShell continuerà. Una volta completato, è possibile chiudere PowerShell. La configurazione è completa.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

A questo punto, i servizi dovrebbero venire avviati automaticamente e l'agente monitorerà e raccoglierà i dati. Tenere presente che, se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, verranno visualizzati avvisi nella finestra di PowerShell. La schermata seguente è un esempio.

![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Per verificare che l'agente sia stato installato, aprire i servizi e cercare quanto segue. Questi servizi dovrebbero essere in esecuzione se la configurazione è stata completata. In caso contrario, non verranno avviati fino al completamento della configurazione.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Verificare Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

<!---HONumber=August15_HO6-->