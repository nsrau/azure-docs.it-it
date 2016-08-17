<properties
	pageTitle="Risoluzione dei problemi relativi all'iscrizione ad Azure | Microsoft Azure"
	description="Risolvere le cause più comuni dei problemi che possono verificarsi durante l'iscrizione ad Azure."
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="cjiang"/>

# Risoluzione dei problemi relativi all'iscrizione ad Azure
Questo articolo descrive come risolvere le cause più comuni dei problemi che possono verificarsi durante l'iscrizione ad Azure.

> [AZURE.NOTE] Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un incidente del supporto tecnico di Azure nel [sito del supporto tecnico di Azure](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409). Per informazioni sull'uso del Supporto tecnico di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

## Errore quando si immettono le informazioni per eseguire l'iscrizione.
Le informazioni relative al profilo dell'account sono corrette? I dettagli del profilo vengono usati per generare gli estratti conto ai fini della fatturazione ed è quindi importante immettere informazioni accurate. Esempi di campi non corretti sono:
- Nome o cognome non corretto.
- Numero di telefono non valido.
- Indirizzo non valido.
- Uso di caratteri non standard.

Suggerimenti:
- immettere le informazioni in modo accurato per eseguire l'iscrizione correttamente.

## Non vengono visualizzati messaggi di testo o chiamate durante la verifica dell'account quando si tenta di eseguire l'iscrizione.
- Verificare che il numero di telefono possa ricevere SMS.
- Verificare il numero di telefono immesso, inclusa la selezione del prefisso internazionale nel menu a discesa.
- Assicurarsi che il telefono possa ricevere messaggi SMS se si sceglie l'opzione "Invia SMS" o le telefonate se si sceglie l'alternativa "Chiama".
- Se si usa un telefono cellulare, verificare che il segnale della rete cellulare sia buono.
- Attendere fino a 4 minuti per consentire al sistema di messaggistica di inviare il codice di testo se si sceglie l'opzione "Invia SMS".
- Non appena si riceve l'SMS, inserire il codice nella casella di testo e fare clic sul pulsante di verifica per continuare.

Suggerimenti:
- se non si ricevono messaggi di testo (SMS) nel telefono, usare il metodo di verifica alternativo "chiama utente".
- Se il passaggio di verifica telefonica non riesce né tramite SMS, né tramite il metodo "chiama utente", usare un altro numero di telefono.
- Non è possibile usare un numero di telefono VoIP per il processo di verifica tramite telefono.

**Nota:** è possibile modificare il numero di telefono preferito in un secondo momento aggiornando [le informazioni del profilo](https://account.windowsazure.com/Profile).

## La carta di credito non è stata accettata.
Assicurarsi che il metodo di pagamento indicato durante la procedura di iscrizione sia supportato per le attivazioni o i pagamenti di Azure.
- Le carte di credito/debito virtuali e prepagate non sono accettate.
- I fornitori di carte di credito/debito accettati variano in base al paese dell'account.

Suggerimenti:
- Per le cause più comuni dei problemi relativi all'iscrizione tramite carta di credito o di debito, vedere l'articolo [La carta di credito non viene accettata quando si tenta di iscriversi ad Azure](billing-credit-card-fails-during-azure-sign-up.md).

## Non è possibile attivare un piano con vantaggi di Azure, ad esempio MSDN, BizSpark, BizSparkPlus o MPN.
Verificare lo stato di idoneità: verificare tramite il canale del programma dei vantaggi se si è idonei al piano scelto:
- MSDN:
  - Verificare lo stato di idoneità nella [pagina dell'account MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Se non è possibile verificare lo stato, contattare i [centri del Servizio clienti per abbonamenti MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
- MPN
  - Accedere al [portale di MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) e verificare lo stato di idoneità. È possibile usufruire di vantaggi aggiuntivi se si hanno le [competenze Cloud Platform](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx) appropriate.
  - Se non è possibile verificare lo stato, contattare il [supporto tecnico di MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
- BizSpark:
  - Accedere al [portale di BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) e verificare lo stato di idoneità per BizSpark e BizSpark Plus. Se non è possibile verificare lo stato, contattare il supporto di BizSpark con un messaggio di posta elettronica facendo clic su [Contatta il team di BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

Suggerimenti:
- Se si è tentato di attivare una nuova sottoscrizione tramite i vantaggi e sono stati riscontrati errori durante l'esperienza di iscrizione, verificare se l'installazione della sottoscrizione è stata completata nella [pagina di sottoscrizione di Azure](http://account.windowsazure.com/Subscriptions). Potrebbero essere necessari alcuni minuti prima che la sottoscrizione risulti attiva. Una volta attivata la sottoscrizione, si riceverà un messaggio di posta elettronica. Se lo stato della sottoscrizione resta in sospeso per più di quattro minuti, si [contattare il supporto di Azure](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) per richiedere assistenza.

## Non è possibile attivare una nuova sottoscrizione di Azure In Open.
È necessaria una chiave di attivazione del servizio online (OSA, Online Service Activation) a cui è associato almeno un token di Azure In Open per attivare una nuova sottoscrizione di Azure In Open.

Suggerimenti:
- Se non è disponibile una chiave di attivazione del servizio online, contattare uno dei partner Microsoft elencati in [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## Non è possibile attivare una versione di valutazione gratuita di Azure.
È già stata usata una sottoscrizione di Azure in passato? Le condizioni per l'utilizzo di Azure prevedono un limite di una sola attivazione della versione di valutazione gratuita per ogni utente che non ha mai usato Azure in precedenza. Se quindi si è già usato un qualsiasi altro tipo di sottoscrizione di Azure, non sarà possibile attivare una versione di valutazione gratuita.

Suggerimenti:
- se è già stata attivata una sottoscrizione di Azure in passato e l'attivazione della versione di valutazione gratuita non riesce, prendere in considerazione l'acquisto di una sottoscrizione con pagamento in base al consumo. L'utente potrebbe inoltre aver diritto a un'offerta con vantaggi. Per altre informazioni, vedere la [pagina dettagli dell'offerta Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) anziché il piano della versione di valutazione gratuita di Azure. Si noti che i piani con vantaggi richiedono prerequisiti specifici.

<!---HONumber=AcomDC_0803_2016-->