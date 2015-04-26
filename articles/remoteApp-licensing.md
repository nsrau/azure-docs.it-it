<properties title="Azure RemoteApp licensing" pageTitle="Licenze di Azure RemoteApp" description="Informazioni sulle licenze di Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Funzionamento delle licenze in Azure RemoteApp


È stato configurato il servizio Azure RemoteApp, sono stati creati i modelli e si è ora pronti per pubblicare le app per gli utenti. Ma c'è ancora un aspetto da prendere in considerazione: le licenze. Come funzionano le licenze per le app che si condividono tramite RemoteApp?

È possibile offrire qualsiasi app o programma per cui si dispone di licenza. 

Con la sottoscrizione di Azure RemoteApp non sarà necessario acquistare licenze di Windows e/o licenze CAL per Desktop remoto, poiché sono tutte già incluse nel prezzo della sottoscrizione.

Se si vuole usare l'immagine modello di Office 365 fornita con Azure RemoteApp, è necessario disporre di un piano *esistente* di Office 365 ProPlus. Lo stesso vale per qualsiasi app di Office 365 pubblicate usando un modello personalizzato. È necessario attivare le app usando la propria sottoscrizione. Questo vale sia per le sottoscrizioni di valutazione che per quelle a pagamento. Se si vuole usare l'immagine modello di Office 365 durante la valutazione, *e non si dispone già di una sottoscrizione*, andare alla pagina di Office 365 per [iscriversi](https://go.microsoft.com/fwlink/p/?LinkID=403802) per una sottoscrizione di valutazione. Per altre informazioni, vedere la pagina relativa al [funzionamento di RemoteApp e Office in combinazione](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-o365/).

Se durante il periodo di valutazione non si vuole ottenere una sottoscrizione di valutazione di Office 365, usare l'immagine modello di Office 2013 Professional Plus fornita con RemoteApp. Questa immagine modello può essere usata solo per 30 giorni e non può essere convertita in una raccolta a pagamento. Al termine del periodo di valutazione, sarà necessario eliminare la raccolta e crearne una nuova per poter continuare a usare Office 2013.  

Riassumendo, è possibile pubblicare qualsiasi app di cui si detiene il diritto legale alla condivisione. È quindi necessario assicurarsi di avere effettivamente il diritto di condividere i propri programmi.

Si noti che non è possibile usare una licenza CAL o un contratto multilicenza in una raccolta nel cloud. Si *può* usare un contratto multilicenza per attivare applicazioni in una raccolta ibrida (ad eccezione di Office). È sufficiente installarle nell'immagine modello dai supporti forniti per contratti multilicenza. Seguire le indicazioni del fornitore dell'applicazione per installare le licenze in un ambiente Desktop remoto.

<!--HONumber=35.2-->
