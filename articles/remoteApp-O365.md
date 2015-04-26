<properties title="Using Office365 with Azure RemoteApp" pageTitle="Usare Office365 con Azure RemoteApp" description="Informazioni sull'interazione tra Office 365 e Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Usare Office365 con Azure RemoteApp

Sono disponibili due opzioni per l'hosting di applicazioni di Office in RemoteApp: Office 365 ProPlus o la versione di valutazione di Office 2013 Professional Plus.

##Office 365 ProPlus 
È possibile creare una raccolta RemoteApp usando l'immagine modello di Office 365 ProPlus. Questa opzione consente di estendere il servizio di Office 365 a RemoteApp. È necessario disporre di un piano di sottoscrizione esistente e gli utenti devono essere dotati di licenza per il servizio Office 365 ProPlus, autonoma o tramite i piani di servizio di Office 365. Le istanze di RemoteApp delle app di Office verranno considerate come una delle cinque istanze di installazione consentite per ogni utente e verranno completamente attivate. È possibile gestire le licenze di Office 365 degli utenti nel [portale dell'amministratore di Office 365](https://portal.office365.com/). Per altre informazioni, vedere la pagina relativa ai [piani dei servizi di Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  

L'opzione Office 365 ProPlus è disponibile sia durante i primi 30 giorni di valutazione gratuita che durante il successivo uso in modalità di produzione, ma è l'unica opzione supportata dopo la scadenza della versione di valutazione.  

Si noti che è anche possibile creare un'immagine modello personalizzata contenente Office 365 ProPlus. Per creare un'immagine modello, seguire i [passaggi di distribuzione](http://technet.microsoft.com/it-it/library/dn782858(v=office.15).aspx) per Office 365 ProPlus su Servizi Desktop remoto.

##Versione di valutazione di Office 2013 Professional Plus 
Durante la valutazione di 30 giorni di RemoteApp, è possibile usare l'immagine modello di Office 2013 Professional Plus (versione di valutazione) per creare una raccolta RemoteApp. È possibile assegnare utenti a questa versione di valutazione della raccolta usando i rispettivi account di lavoro di Azure Active Directory oppure gli account Microsoft. Non è necessario alcun genere di sottoscrizione aggiuntiva.

Si tratta di un'ottima opzione per provare e toccare con mano i vantaggi di Office in RemoteApp. Questa opzione è però solo prevista per scopi di valutazione e di test. Le raccolte RemoteApp create usando l'immagine modello di Office 2013 Professional Plus (versione di valutazione) non possono essere trasferite alla modalità di produzione e verranno disabilitate al termine del periodo di valutazione.

##Passaggio dalla versione di valutazione alla modalità di produzione
Quando si avvia la versione di valutazione gratuita di 30 giorni, una nota nella sezione RemoteApp del portale indicherà il tempo di valutazione rimanente prima che sia necessario passare a un account a pagamento. È possibile attivare l'account e passare alla modalità di produzione usando l'apposito collegamento in questa nota. 

Quando si attiva l'account, l'operazione avrà effetto su tutte le raccolte RemoteApp presenti nell'account stesso. 

- Le raccolte che vengono eseguite con Windows Server 2012 R2 o con le immagini modello di Office 365 ProPlus passeranno facilmente e trasparentemente alla modalità di produzione. Tutti i dati e le impostazioni utente, comprese le sessioni in corso, rimarranno invariate.
- Se sono state caricate immagini modello personalizzate, anche la transizione delle raccolte che usano tali immagini avverrà facilmente e in modo trasparente.
- L'immagine modello di Office 2013 Professional Plus (versione di valutazione) è destinata esclusivamente alla valutazione. Le raccolte in cui si esegue questa immagine modello non possono essere passate alla modalità di produzione. Queste raccolte verranno messe in stato di disabilitazione.


Se non si esegue la transizione alla modalità di produzione entro la scadenza della versione di valutazione, le raccolte RemoteApp verranno disabilitate. Ciò non sarà comunque un problema: le impostazioni e i dati degli utenti verranno salvati per altri 90 giorni, in modo che sia comunque possibile attivare il servizio e passare alla modalità di produzione senza alcuna perdita di dati.

<!--HONumber=35.2-->
