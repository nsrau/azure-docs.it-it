<properties 
    pageTitle="Licenze di Azure RemoteApp" 
    description="Informazioni sul funzionamento delle licenze in Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />


# Funzionamento delle licenze in Azure RemoteApp


È stato configurato il servizio Azure RemoteApp, sono stati creati i modelli e si è ora pronti per pubblicare le app per gli utenti. Ma c'è ancora un aspetto da prendere in considerazione: le licenze. Come funzionano le licenze per RemoteApp e per le app condivise tramite RemoteApp?

RemoteApp non richiede licenze Windows o licenze CAL per Desktop remoto. La parte RemoteApp è compresa nella sottoscrizione (verificare i dettagli dei [piani dei prezzi](../../../pricing/details/remoteapp/)).

Se si desidera usare l'immagine modello di Office 365 inclusa in RemoteApp, è necessario disporre di un piano *esistente* di Office 365 ProPlus. Lo stesso vale per qualsiasi app di Office 365 pubblicate usando un modello personalizzato. È necessario attivare le app usando la propria sottoscrizione. Questo vale sia per le sottoscrizioni di valutazione che per quelle a pagamento. Se si desidera usare l'immagine modello di Office 365 durante la valutazione *e non si dispone già di una sottoscrizione*, andare alla pagina di Office 365 per [iscriversi](https://go.microsoft.com/fwlink/p/?LinkID=403802) per una sottoscrizione di valutazione. Per altre informazioni, vedere la pagina relativa al [funzionamento di RemoteApp e Office in combinazione](remoteapp-o365.md).

Se durante il periodo di valutazione non si vuole ottenere una sottoscrizione di valutazione di Office 365, usare l'immagine modello di Office 2013 Professional Plus fornita con RemoteApp. Questa immagine modello può essere usata solo per 30 giorni e non può essere convertita in una raccolta a pagamento.

Per le altre app, è necessario assicurarsi di disporre della licenza per condividere l'app.

Riassumendo, è possibile pubblicare qualsiasi app di cui si detiene il diritto legale alla condivisione. È quindi necessario assicurarsi di avere effettivamente il diritto di condividere i propri programmi.

Si noti che non è possibile usare una licenza CAL o un contratto multilicenza in una raccolta nel cloud. È *possibile* usare un contratto multilicenza per attivare applicazioni nella raccolta ibrida (ad eccezione di Office). È sufficiente installarle nell'immagine modello dai supporti forniti per contratti multilicenza. Seguire le indicazioni del fornitore dell'applicazione per installare le licenze in un ambiente Desktop remoto.
 

<!---HONumber=August15_HO6-->