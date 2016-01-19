<properties
    pageTitle="Cosa è incluso nelle immagini modello di Azure RemoteApp | Microsoft Azure"
    description="Informazioni sulle immagini modello incluse con Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="01/07/2016"
    ms.author="elizapo" />

# Cosa è incluso nelle immagini modello di Azure RemoteApp

La sottoscrizione di Azure RemoteApp include tre immagini modello:


- Windows Server 2012
- Microsoft Office 365 ProPlus (Richiede una sottoscrizione di Office 365)
- Microsoft Office 2013 Professional Plus (solo versione di valutazione)

> [AZURE.IMPORTANT]La sottoscrizione di Azure RemoteApp consente di accedere al software nell'immagine, ad eccezione di Office 365 ProPlus, che richiede una sottoscrizione separata e Office 2013, non può essere usato nell'ambiente di produzione. Ciò significa che è possibile condividere i programmi o le app nelle immagini modello con gli utenti. Ad esempio, se si crea una raccolta che usa l'immagine di Windows Server 2012 R2, è possibile pubblicare System Center Endpoint Protection per consentire agli utenti di accedere mediante RemoteApp.
>
> Per altre informazioni, consultare la pagina relativa ai [dettagli sulle licenze di RemoteApp](remoteapp-licensing.md). Oltre a [Utilizzo di Office con Azure RemoteApp](remoteapp-o365.md) per le licenze di Office.

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
- Word
- Strumenti di correzione di Microsoft Office

L'immagine include inoltre Visio Pro e Project Pro.

E anche le applicazioni seguenti:

- SQL Native client
- Driver ODBC
- Client di data mining di SQL Server
- Client MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


La piena funzionalità delle app di Office 365 ProPlus è disponibile solo per gli utenti che dispongono di un piano di Office 365 ProPlus. Per altre informazioni sui piani di sottoscrizione di Office 365, vedere la pagina relativa ai [piani di servizio di Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Altre domande? Consultare le informazioni di [Office 365 + RemoteApp](remoteapp-o365.md). Consultare inoltre questo articolo, [Come usare l'abbonamento a Office 365 con Azure RemoteApp](remoteapp-officesubscription.md).

Si noti che è necessario concedere in licenza Office 365 ProPlus, Visio Pro e Project Pro separatamente, - ciascuno ha le propria licenze.

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

> [AZURE.IMPORTANT]**Informazioni legali:** questa immagine non include una licenza di Microsoft Office e *non può essere usata per ambienti di produzione*. L'immagine di Office 2013 Professional Plus è destinata esclusivamente alla valutazione. Se si desidera usare le applicazioni Office in Azure RemoteApp per la produzione, è necessario usare l'immagine di Office 365 ProPlus. Per altre informazioni sulla licenza di Office, vedere [Usare Office 365 con Azure RemoteApp](remoteapp-o365.md).

<!---HONumber=AcomDC_0114_2016-->