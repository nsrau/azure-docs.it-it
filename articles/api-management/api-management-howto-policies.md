<properties 
	pageTitle="Criteri in Gestione API di Azure" 
	description="Informazioni su come creare, modificare e configurare criteri in Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="sdanie"/>


#Criteri in Gestione API di Azure

In Gestione API di Azure i criteri sono una potente funzionalità del sistema che consentono all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale in caso di richiesta o risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare la quantità di chiamate in ingresso da uno sviluppatore. Sono disponibili molti altri criteri predefiniti.

Per un elenco completo di istruzioni dei criteri e delle relative impostazioni, vedere [Informazioni di riferimento per i criteri][].

I criteri vengono applicati nel proxy che si trova tra il consumer di API e l'API gestita. Il proxy riceve tutte le richieste e in genere le inoltra invariate all'API sottostante. Tuttavia i criteri possono applicare modifiche sia alla richiesta in ingresso che alla risposta in uscita.

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, a meno che i criteri non specifichino diversamente. Alcuni criteri, come [choose][] e [set-variable][], sono basati su espressioni di criteri. Per altre informazioni, vedere [Criteri avanzati][] ed [Espressioni di criteri][].

## <a name="scopes"> </a>Come configurare criteri
I criteri possono essere configurati a livello globale o nell'ambito di un [prodotto][], un'[API][] o un'[operazione][]. Per configurare i criteri, passare all'editor dei criteri nel portale di pubblicazione.

![Manu Criteri][policies-menu]

L'editor dei criteri comprende tre sezioni principali: l'ambito criteri (in alto), la definizione criteri in cui i criteri vengono modificati (a sinistra) e l'elenco di istruzioni (a destra).

![Editor criteri][policies-editor]

Per iniziare a configurare i criteri, prima è necessario selezionare l'ambito in cui applicare i criteri. Nella schermata seguente è selezionato il prodotto Starter. Il quadratino accanto al nome del criterio indica che un criterio è già applicato a questo livello.

![Ambito][policies-scope]

Poiché è già stato applicato un criterio, la configurazione viene mostrata nella visualizzazione definizione.

![Configurare][policies-configure]

Il criterio viene dapprima visualizzato come di sola lettura. Per modificare la definizione, fare clic sull'azione di **configurazione dei criteri**.

![Modificare][policies-edit]

La definizione criteri è un semplice documento XML che descrive una sequenza di istruzioni in ingresso e in uscita. Il codice XML può essere modificato direttamente nella finestra della definizione. Un elenco di istruzioni è disponibile a destra e le istruzioni applicabili all'ambito corrente sono abilitate ed evidenziate, come ad esempio l'istruzione Limita frequenza chiamate nella schermata precedente.

Facendo clic su un'istruzione abilitata, il codice XML appropriato verrà aggiunto in corrispondenza del cursore nella visualizzazione definizione.

Un elenco completo di istruzioni dei criteri e le relative impostazioni sono disponibili in [Informazioni di riferimento per i criteri][].

Ad esempio, per aggiungere una nuova istruzione per limitare le richieste in arrivo agli indirizzi IP specificati, posizionare il cursore nel contenuto dell'elemento XML "inbound" e fare clic sull'istruzione Limita IP chiamanti.

![Criteri di restrizione][policies-restrict]

Verrà aggiunto un frammento XML all'elemento "inbound" che fornisce informazioni aggiuntive sulla configurazione dell'istruzione.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

Per limitare le richieste in ingresso e accettare solo quelle da un indirizzo IP 1.2.3.4, modificare il codice XML nel modo seguente:

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Salva][policies-save]

Dopo aver configurato le istruzioni per il criterio, fare clic su Salva per propagare immediatamente le modifiche al proxy di Gestione API.

##<a name="sections"> </a>Informazioni sulla configurazione dei criteri

Un criterio è una serie di istruzioni eseguite in un determinato ordine in relazione a una richiesta e una risposta. La configurazione è correttamente suddivisa in un elemento inbound (richiesta) e un elemento outbound (criterio) come mostrato nella configurazione.

	<policies>
		<inbound>
			<!-- statements to be applied to the request go here -->
		</inbound>
		<outbound>
			<!-- statements to be applied to the response go here -->
		</outbound>
	</policies>

Poiché i criteri possono essere specificati a livelli diversi (globale, prodotto, API e operazione), la configurazione consente di specificare l'ordine in cui le istruzioni di questa definizione vengono eseguite rispetto al criterio padre.

Ad esempio, se ci sono un criterio a livello globale e un criterio configurato per un'API, quando questa particolare API viene usata, vengono applicati entrambi i criteri. Gestione API consente l'ordinamento deterministico delle istruzioni combinate per i criteri attraverso l'elemento di base.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

Nella definizione del criterio dell'esempio precedente, l'istruzione cross-domain verrà eseguita prima di un criterio di livello superiore che verrà a sua volta seguito dal criterio find-and-replace.

Nota: con i criteri globali non sono disponibili criteri padre e l'uso dell'elemento `<base>` in tali criteri non produce alcun effetto.

## Passaggi successivi

Vedere il video seguente sulle espressioni di criteri.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Informazioni di riferimento per i criteri]: api-management-policy-reference.md
[prodotto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis
[operazione]: api-management-howto-add-operations.md

[Criteri avanzati]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[choose]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[set-variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Espressioni di criteri]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!---HONumber=July15_HO4-->