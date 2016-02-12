<properties
	pageTitle="Domande frequenti su impostazioni e dati in roaming | Microsoft Azure"
	description="Fornisce le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app."
	services="active-directory"
    keywords="impostazioni di enterprise state roaming, cloud windows, domande frequenti su enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="femila"/>

# Domande frequenti su impostazioni e dati in roaming

Questo argomento fornisce le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app.

## Quali tipi di dati sono disponibili in roaming?
**Impostazioni di Windows**: impostazioni del computer disponibili nel sistema operativo Windows. In genere, si tratta delle impostazioni che personalizzano il computer dell'utente e includono le categorie generiche seguenti:

- **Tema**: tema del desktop, impostazioni della barra delle applicazioni e così via. 
- **Impostazioni di Internet Explorer**: schede aperte di recente, Preferiti e così via.
- **Impostazioni del browser Edge**: Preferiti, elenco di lettura.
- **Password**: password Internet, profili Wi-Fi e così via. 
- **Preferenze della lingua**: layout di tastiera, lingua del sistema, data e ora e così via. 
- **Accessibilità**: tema a contrasto elevato, Assistente vocale, Lente di ingrandimento e così via. 
- **Altre impostazioni di Windows**: impostazioni del prompt dei comandi, elenco applicazioni e così via. 

**Dati applicazione**: le app di Windows universale possono scrivere i dati delle impostazioni in una cartella di "roaming" ed eventuali dati scritti in questa cartella verranno sincronizzati automaticamente. Il singolo sviluppatore di app deve progettare un'app in grado di sfruttare questa funzionalità. Per altre informazioni su come sviluppare un'app di Windows universale che usa il roaming, vedere la pagina relativa all'[API di archiviazione dei dati dell'app](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e il [blog per gli sviluppatori di dati in roaming di app Windows 8](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## Quale account si usa per la sincronizzazione delle impostazioni?
In Windows 8 e Windows 8.1 la sincronizzazione delle impostazione usa sempre gli account Microsoft per utenti. Gli utenti aziendali possono connettere un account Microsoft al proprio account di dominio di Active Directory per ottenere l'accesso alla sincronizzazione delle impostazioni. In Windows 10 questa funzionalità relativa a un "account Microsoft connesso" viene sostituita da un framework di account primario/secondario.
 
L'account primario viene definito come l'account usato per accedere a Windows. Può essere un account Microsoft, un account Azure Active Directory (Azure AD), un account Active Directory locale o un account locale. Oltre all'account primario, gli utenti di Windows 10 possono aggiungere uno o più account cloud secondari al proprio dispositivo. Questi account secondari sono in genere un account Microsoft, un account Azure Active Directory o un account di altro tipo, ad esempio Gmail o Facebook. Questi account secondari forniscono l'accesso a servizi aggiuntivi, ad esempio l'accesso Single Sign-On e Windows Store, ma non consentono la sincronizzazione delle impostazioni.

In Windows 10 solo l'account primario per il dispositivo può essere usato per la sincronizzazione delle impostazioni. Per informazioni sulle eccezioni, vedere l'articolo relativo alla procedura di aggiornamento dalla sincronizzazione delle impostazioni dell'account Microsoft in Windows 8 alla sincronizzazione delle impostazioni di Azure AD in Windows 10.

I dati non vengono mai combinati tra i diversi account utente nel dispositivo. Esistono due regole per la sincronizzazione delle impostazioni: - Il roaming delle impostazioni di Windows verrà sempre eseguito con l'account primario. - I dati dell'app verranno contrassegnati con l'account usato per acquisire l'app. Verranno sincronizzate solo le app contrassegnate con l'account primario. Il contrassegno della proprietà dell'app viene determinato quando un'app viene installata tramite Windows Store o quando viene trasferita localmente tramite gestione di dispositivi mobili.

Se non è possibile identificare il proprietario di un'app, il roaming dell'app verrà eseguito con l'account primario. Se un dispositivo viene aggiornato da Windows 8 o Windows 8.1 a Windows 10, tutte le app verranno contrassegnate come acquisite dall'account Microsoft perché, in genere, la maggior parte delle app è stata acquisita tramite Windows Store e non è disponibile alcun supporto per Windows Store per account Azure AD prima di Windows 10. Se un'app viene installata tramite una licenza offline, l'app verrà contrassegnata usando l'account primario nel dispositivo.

>[AZURE.NOTE]  
La possibilità di connettere un account Microsoft a un account di dominio e di sincronizzare tutti i dati dell'utente nell'account Microsoft (ad esempio roaming dell'account Microsoft tramite la funzionalità "account Microsoft connesso e Active Directory") viene rimossa dai dispositivi Windows 10 aggiunti a un ambiente Active Directory/Azure AD connesso.
 
## Come si esegue l'aggiornamento dalla sincronizzazione delle impostazioni dell'account Microsoft in Windows 8 alla sincronizzazione delle impostazioni di Azure AD in Windows 10?
Un utente aggiunto al dominio di Active Directory che esegue Windows 8 o Windows 8.1 con un account Microsoft connesso potrà sincronizzare le impostazioni tramite il proprio account Microsoft. Dopo l'aggiornamento a Windows 10, gli utenti aggiunti al dominio continueranno a sincronizzare le impostazioni utente tramite l'account Microsoft, purché il dominio di Active Directory non sia connesso ad Azure AD. Se il dominio di Active Directory locale si connette ad Azure AD, il dispositivo dell'utente inizierà a provare a sincronizzare le impostazioni usando l'account Azure AD connesso. Se l'amministratore di Azure AD non abilita il servizio Enterprise State Roaming, gli utenti con un account Azure AD connesso non potranno più sincronizzare le impostazioni. Dopo l'abilitazione della sincronizzazione delle impostazioni tramite Azure AD, gli utenti di Windows 10 inizieranno immediatamente a sincronizzare le impostazioni di Windows con Azure AD se l'utente accede con un'identità di Azure AD.

Gli utenti che hanno archiviato dati personali nel proprio dispositivo aziendale devono essere consapevoli del fatto che inizierà la sincronizzazione con Azure AD dei dati del sistema operativo Windows e dell'applicazione. Ciò comporta le implicazioni seguenti: - Le impostazioni degli utenti con account Microsoft personali si "discosteranno" lentamente dalle impostazioni negli account Azure AD aziendali, perché per la sincronizzazione delle impostazioni dell'account Microsoft e di Azure AD vengono usati account separati. - I dati personali, ad esempio password per Wi-Fi, credenziali Web e Preferiti e schede di Internet Explorer sincronizzati precedentemente tramite un account Microsoft connesso, verranno sincronizzati tramite Azure AD.


## Come funziona l'interoperabilità tra l'account Microsoft e il servizio Enterprise State Roaming di Azure AD? 
Nelle versioni del 2015 di Windows 10 il servizio Enterprise State Roaming è supportato solo per un account alla volta. Se l'utente accede a Windows usando un account Azure AD dell'organizzazione, tutti i dati verranno sincronizzati tramite Azure AD. Se l'utente accede a Windows usando un account Microsoft personale, tutti i dati verranno sincronizzati tramite l'account Microsoft. Il roaming dei dati dell'app verrà eseguito mediante l'account di accesso primario nel dispositivo se la licenza dell'app è di proprietà dell'account primario. I dati delle app di proprietà di account secondari non verranno sincronizzati in Windows 10.

## È possibile sincronizzare le impostazioni per gli account Azure AD da più tenant?
Quando più account Azure AD da diversi tenant di Azure AD si trovano sullo stesso dispositivo, è necessario aggiornare il Registro di sistema del dispositivo per comunicare con il servizio Azure Rights Management (RMS) per ogni tenant di Azure AD.

1. È prima di tutto necessario il GUID per ogni tenant di Azure AD. Aprire il portale di Azure classico e selezionare un tenant di Azure AD. Il GUID per il tenant si trova nell'URL nella barra degli indirizzi del browser, come indicato di seguito: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Quando il GUID è disponibile, sarà necessario aggiungere la chiave seguente del Registro di sistema: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<GUID ID tenant>**. Dalla chiave <**GUID ID tenant**> creare un nuovo valore multistringa (REG-MULTI-SZ) denominato **AllowedRMSServerUrl** e per i rispettivi dati specificare gli URL del punto di distribuzione degli altri tenant di Azure a cui accede il dispositivo. 
3. Per trovare gli URL del punto di distribuzione delle licenze, eseguire il cmdlet **Get-AadrmConfiguration**. Se i valori per **LicensingIntranetDistributionPointUrl **e **LicenseingExtranetDistributionPointUrl** sono diversi, specificarli entrambi. Se i valori sono uguali, specificare il valore solo una volta.


## Quali sono le opzioni disponibili per le impostazioni di roaming per le applicazioni di Windows classiche?
Il roaming è disponibile solo per le app di Windows universale. Sono disponibili due opzioni per l'abilitazione del roaming in un'applicazione di Windows classica:

- Usando Project Centennial, è possibile convertire con facilità un'applicazione di Windows classica in un'app di Windows universale. Lo sviluppatore dell'app dovrà apportare modifiche minime al codice per potere sfruttare i vantaggi del roaming dei dati delle app di Azure AD. Questo è il percorso consigliato per abilitare il roaming dei dati dell'app in Win32. 
- Usando il generatore di modelli [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx), è possibile creare un modello di impostazioni personalizzato e applicarlo all'app a ogni avvio. Questa opzione non richiede allo sviluppatore di app di cambiare il codice dell'app, ma nelle versioni del 2015 di Windows 10 il generatore di modelli UE-V è limitato al roaming di Active Directory locale per i clienti che hanno acquistato il Microsoft Desktop Optimization Package. In futuro è possibile che Microsoft esamini eventuali soluzioni per integrare strettamente UE-V in Windows ed estendere UE-V per il roaming delle impostazioni tramite il cloud di Azure AD. 

## È possibile archiviare localmente le impostazioni e i dati sincronizzati?
Il servizio Enterprise State Roaming è stato progettato per archiviare i dati delle impostazioni sul cloud di Azure. UE-V offre una soluzione di roaming locale per Windows 10.

## Chi gestisce i dati sottoposti a roaming?
L'amministratore del tenant gestirà i dati, che verranno archiviati in un data center di Azure. Tutti i dati utente vengono crittografati sul cloud quando sono in transito e quando sono inattivi mediante Azure Rights Management (Azure RMS). Questo rappresenta un miglioramento rispetto alla sincronizzazione delle impostazioni basata sugli account Microsoft, in cui solo alcuni dati sensibili, ad esempio le credenziali dell'utente, vengono crittografati prima di lasciare il dispositivo.

Microsoft si impegna a proteggere i dati dei clienti. I dati delle impostazioni di un utente aziendale vengono crittografati automaticamente da Azure RMS quando lasciano un dispositivo Windows 10, quindi gli altri utenti non possono leggere tali dati. Se l'organizzazione ha una sottoscrizione a pagamento per Azure RMS, è possibile usare altre funzionalità di Azure RMS, ad esempio il rilevamento e la revoca di documenti, la protezione automatica di messaggi di posta elettronica che contengono informazioni riservate e la gestione delle proprie chiavi, ovvero la soluzione BYOK ("Bring Your Own Key"). Per altre informazioni su queste funzionalità e sul funzionamento di Azure RMS, vedere [Informazioni su Microsoft Azure Rights Management](https://technet.microsoft.com/jj585026.aspx).

## È possibile gestire la sincronizzazione per un'app o un'impostazione specifica?
In Windows 10 non è disponibile alcuna gestione di dispositivi mobili o alcuna impostazione di Criteri di gruppo per disabilitare il roaming per una singola app. Gli amministratori del tenant possono disabilitare la sincronizzazione dei dati dell'app per tutte le app in un dispositivo gestito, ma non è disponibile alcun controllo più preciso a livello di singola app o all'interno delle app.

## Che cosa possono fare i singoli utenti per abilitare/disabilitare il roaming?
Nell'app Impostazioni passare ad Account -> Sincronizza impostazioni. In questa pagina è possibile visualizzare gli account usati per il roaming delle impostazioni e abilitare o disabilitare singoli gruppi di impostazioni da sottoporre a roaming.
 
##Quali sono i consigli di Microsoft per l'abilitazione del roaming in Windows 10? 
Microsoft offre alcune soluzioni per il roaming delle impostazioni, inclusi Profili utente mobili, UE-V e il roaming di Azure AD. Se l'organizzazione non è pronta o non approva lo spostamento dei dati sul cloud, Microsoft consiglia di usare UE-V come tecnologia di roaming primaria. Se l'organizzazione richiede il supporto per il roaming per applicazioni di Windows classiche, ma vuole passare al cloud, Microsoft consiglia di usare la sincronizzazione delle impostazioni aziendali e UE-V. Anche se UE-V e la sincronizzazione delle impostazioni aziendali sono tecnologie molto simili, non si escludono a vicenda e attualmente si integrano per assicurare che l'organizzazione offra i servizi di roaming richiesti dagli utenti. Quando si usano la sincronizzazione delle impostazioni aziendali e UE-V, vengono applicate le regole seguenti:

- Il servizio Enterprise State Roaming è l'agente di roaming principale nel dispositivo. UE-V viene usato per integrare il "divario Win32". 
- Il roaming di UE-V per le impostazioni di Windows e per i dati delle app UWP moderne deve essere disabilitato, perché questi elementi rientrano già nel servizio Enterprise State Roaming. 

## Che cosa succede quando la mia organizzazione acquista Azure RMS dopo avere usato il roaming per un determinato periodo di tempo? 
Se l'organizzazione usa già il roaming in Windows 10 con la sottoscrizione gratuita di Azure RMS, l'acquisto di una sottoscrizione di Azure RMS a pagamento non avrà alcun impatto sulla funzionalità del roaming e l'amministratore IT non dovrà apportare alcuna modifica alla configurazione.

## Problemi noti

- L'accesso tramite smart card o smart card virtuale a Windows provoca l'interruzione del funzionamento della sincronizzazione delle impostazioni. Se si prova ad accedere al dispositivo usando una smart card o una smart card virtuale, la sincronizzazione non funzionerà più. È possibile che questo problema venga risolto da aggiornamenti futuri a Windows 10.
- Si verifica un problema con la sincronizzazione dei **Preferiti** in Internet Explorer. La sincronizzazione dei Preferiti di Internet Explorer è temporaneamente disabilitata per evitare altri problemi. Questo problema verrà risolto da aggiornamenti futuri a Windows 10.


## Argomenti correlati
- [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->