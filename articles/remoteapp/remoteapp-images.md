<properties
    pageTitle="Cosa è incluso nelle immagini modello di RemoteApp"
    description="Informazioni sulle immagini modello incluse con RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/13/2015" 
    ms.author="elizapo" />

# Cosa è incluso nelle immagini modello di RemoteApp

La sottoscrizione di Azure RemoteApp include tre immagini modello:


- Windows Server 2012
- Microsoft Office 365 ProPlus (Richiede una sottoscrizione di Office 365)
- Microsoft Office 2013 Professional Plus (solo versione di valutazione)

> [AZURE.IMPORTANT]A prescindere dall'immagine che si usa, è necessario valutare le implicazioni relative alle licenze ogni volta che si condivide un'app con i propri utenti. Per altre informazioni, vedere la [pagina relativa ai dettagli sulle licenze di RemoteApp](remoteapp-licensing.md).

Di seguito sono riportati i dettagli sul contenuto di ogni immagine.

## Windows Server 2012 R2 (immagine "vanilla")
Questa immagine si basa sul sistema operativo Microsoft Windows Server 2012 R2 Datacenter e ha i seguenti ruoli e funzionalità installati per soddisfare i requisiti per le immagini modello di Azure RemoteApp:


- .NET Framework 4.5, 3.5.1, 3.5
- Esperienza desktop
- Servizi di riconoscimento della grafia
- Media Foundation
- Host sessione Desktop remoto.
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Supporto WoW64

Nell'immagine sono anche installate le applicazioni seguenti:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (sottoscrizione necessaria)
Office 365 è l'applicazione più richiesta ed è pertanto stata creata un'immagine personalizzata pronta per l'uso.

Si tratta di un'estensione dell'immagine "vanilla" e presenta i seguenti componenti di Microsoft Office 365 ProPlus installati, oltre a quelli descritti nell'immagine Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Strumenti di correzione di Microsoft Office

E anche le applicazioni seguenti:

- SQL Native client
- Driver ODBC
- Client di data mining di SQL Server
- Client MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


La piena funzionalità delle app di Office 365 ProPlus è disponibile solo per gli utenti che dispongono di un piano di Office 365 ProPlus. Per altre informazioni sui piani di sottoscrizione di Office 365, vedere la pagina relativa ai [piani di servizio di Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Per altre informazioni sulla gestione delle licenze in RemoteApp, vedere la pagina relativa al [funzionamento delle licenze in Azure RemoteApp](remoteapp-licensing.md).

## Microsoft Office 2013 Professional Plus (solo versione di valutazione)
Durante il periodo di valutazione gratuita, è possibile provare il servizio con l'immagine di Office 2013.

Si tratta di un'estensione dell'immagine "vanilla" e presenta i seguenti componenti di Microsoft Office 2013 Professional Plus installati, oltre a quelli descritti nell'immagine Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Strumenti di correzione di Microsoft Office

> [AZURE.IMPORTANT]**Informazioni legali:** questa immagine non include una licenza di Microsoft Office e *non può essere usata per ambienti di produzione*. L'immagine di Office 2013 Professional Plus è destinata esclusivamente alla valutazione. Se si desidera usare le applicazioni Office in Azure RemoteApp per la produzione, è necessario usare l'immagine di Office 365 ProPlus. Per altre informazioni sulla gestione delle licenze in RemoteApp, vedere la pagina relativa al [funzionamento delle licenze in Azure RemoteApp](remoteapp-licensing.md).
 

<!---HONumber=July15_HO4-->