<properties 
	pageTitle="Criteri in Gestione API di Azure | Microsoft Azure" 
	description="Informazioni su come creare, modificare e configurare criteri in Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="sdanie"/>  


#Criteri in Gestione API di Azure

In Gestione API di Azure i criteri sono una potente funzionalità del sistema che consentono all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale in caso di richiesta o risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare la quantità di chiamate in ingresso da uno sviluppatore. Sono disponibili molti altri criteri predefiniti.

Per un elenco completo di istruzioni dei criteri e delle relative impostazioni, vedere [Informazioni di riferimento per i criteri][].

I criteri vengono applicati nel gateway che si trova tra il consumer di API e l'API gestita. Il gateway riceve tutte le richieste e in genere le inoltra invariate all'API sottostante. Tuttavia i criteri possono applicare modifiche sia alla richiesta in ingresso che alla risposta in uscita.

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri, come [choose][] e [set-variable][], sono basati su espressioni di criteri. Per altre informazioni, vedere [Criteri avanzati][] ed [Espressioni di criteri][].

## <a name="scopes"> </a>Come configurare criteri
I criteri possono essere configurati a livello globale o nell'ambito di un [prodotto][], un'[API][] o un'[operazione][]. Per configurare i criteri, passare all'editor dei criteri nel portale di pubblicazione.

![Manu Criteri][policies-menu]

L'editor dei criteri comprende tre sezioni principali: l'ambito criteri (in alto), la definizione criteri in cui i criteri vengono modificati (a sinistra) e l'elenco di istruzioni (a destra).

![Editor criteri][policies-editor]

Per iniziare a configurare i criteri, prima è necessario selezionare l'ambito in cui applicare i criteri. Nella schermata seguente è selezionato il prodotto **Starter**. Il quadratino accanto al nome del criterio indica che un criterio è già applicato a questo livello.

![Scope][policies-scope]

Poiché è già stato applicato un criterio, la configurazione viene mostrata nella visualizzazione definizione.

![Configurare][policies-configure]

Il criterio viene dapprima visualizzato come di sola lettura. Per modificare la definizione, fare clic sull'azione di **configurazione dei criteri**.

![Modificare][policies-edit]

La definizione criteri è un semplice documento XML che descrive una sequenza di istruzioni in ingresso e in uscita. Il codice XML può essere modificato direttamente nella finestra della definizione. Un elenco di istruzioni è disponibile a destra e le istruzioni applicabili all'ambito corrente sono abilitate ed evidenziate, come ad esempio l'istruzione **Limita frequenza chiamate** nella schermata precedente.

Facendo clic su un'istruzione abilitata, il codice XML appropriato verrà aggiunto in corrispondenza del cursore nella visualizzazione definizione.

>[AZURE.NOTE] Se il criterio che si desidera aggiungere non è abilitato, verificare di essere nell'ambito corretto per il criterio. Ogni istruzione di criterio è progettata per essere usata in determinati ambiti e sezioni dei criteri. Per esaminare le sezioni dei criteri e gli ambiti di un criterio, controllare la sezione relativa all'**utilizzo** del criterio in [Riferimento ai criteri di Gestione API di Azure][].

Un elenco completo di istruzioni dei criteri e le relative impostazioni sono disponibili in [Informazioni di riferimento per i criteri][].

Ad esempio, per aggiungere una nuova istruzione per limitare le richieste in arrivo agli indirizzi IP specificati, posizionare il cursore nel contenuto dell'elemento XML `inbound` e fare clic sull'istruzione **Limita IP chiamanti**.

![Criteri di restrizione][policies-restrict]

Verrà aggiunto un frammento XML all'elemento `inbound` che fornisce informazioni aggiuntive sulla configurazione dell'istruzione.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

Per limitare le richieste in ingresso e accettare solo quelle da un indirizzo IP 1.2.3.4, modificare il codice XML nel modo seguente:

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Save][policies-save]

Dopo aver configurato le istruzioni per il criterio, fare clic su **Salva** per propagare immediatamente le modifiche al gateway di Gestione API.

##<a name="sections"> </a>Informazioni sulla configurazione dei criteri

Un criterio è una serie di istruzioni eseguite in un determinato ordine in relazione a una richiesta e una risposta. La configurazione è correttamente suddivisa nelle sezioni `inbound`, `backend`, `outbound`, e `on-error` come mostrato nella seguente configurazione.

	<policies>
	  <inbound>
	    <!-- statements to be applied to the request go here -->
	  </inbound>
	  <backend>
	    <!-- statements to be applied before the request is forwarded to 
	         the backend service go here -->
	  </backend>
	  <outbound>
	    <!-- statements to be applied to the response go here -->
	  </outbound>
	  <on-error>
	    <!-- statements to be applied if there is an error condition go here -->
	  </on-error>
	</policies> 

Se si verifica un errore durante l'elaborazione di una richiesta, tutti i rimanenti passaggi nelle sezioni `inbound`, `backend`, o `outbound` vengono ignorate e l'esecuzione prosegue con le istruzioni nella sezione`on-error`. Inserendo istruzioni di criteri nella sezione `on-error` è possibile rivedere l'errore utilizzando la proprietà `context.LastError`, esaminare e personalizzare la risposta di errore tramite il criterio `set-body` e configurare che cosa accade se si verifica un errore. Sono disponibili codici di errore per i passaggi incorporati e per gli errori che possono verificarsi durante l'elaborazione delle istruzioni dei criteri. Per altre informazioni, vedere [Gestione degli errori nei criteri di Gestione API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Poiché i criteri possono essere specificati a livelli diversi (globale, di prodotto, di API e di operazione), la configurazione fornisce un modo per specificare l'ordine in cui le istruzioni della definizione del criterio vengono eseguite rispetto al criterio padre.

Gli ambiti dei criteri vengono valutati nell'ordine seguente.

1. Ambito globale
2. Ambito del prodotto
3. Ambito dell’API
4. Ambito dell'operazione

Le istruzioni all'interno di essi vengono valutate in base alla posizione dell’elemento `base`, se presente.

Ad esempio, se ci sono un criterio a livello globale e un criterio configurato per un'API, quando questa particolare API viene usata, vengono applicati entrambi i criteri. Gestione API consente l'ordinamento deterministico delle istruzioni combinate per i criteri attraverso l'elemento di base.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

Nella definizione del criterio dell'esempio precedente, l'istruzione `cross-domain` verrà eseguita prima di un criterio di livello superiore che verrà a sua volta seguito dal criterio `find-and-replace`.

Se lo stesso criterio viene visualizzato due volte nell'istruzione del criterio, viene applicato il criterio che è stato valutato più di recente. È possibile utilizzare questo per sostituire i criteri definiti a un ambito più elevato. Per visualizzare i criteri nell'ambito corrente nell'editor dei criteri, fare clic su **Ricalcolare il criterio effettivo per l'ambito selezionato**.

Notare che i criteri globali non hanno criteri padre e che usando in essi l'elemento `<base>` esso non produce alcun effetto.

## Passaggi successivi

Vedere il video seguente sulle espressioni di criteri.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Informazioni di riferimento per i criteri]: api-management-policy-reference.md
[Riferimento ai criteri di Gestione API di Azure]: api-management-policy-reference.md
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

<!---HONumber=AcomDC_0810_2016-->