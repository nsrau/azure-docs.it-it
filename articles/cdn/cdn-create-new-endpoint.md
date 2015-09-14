<properties 
	 pageTitle="Come abilitare la rete per la distribuzione di contenuti (rete CDN) per Azure"
	description="Questo argomento illustra come abilitare la rete per la distribuzione di contenuti (CDN) per Azure."
	services="cdn"
	documentationCenter=""
	authors="zhangmanling"
	manager="dwrede"
	editor=""/>
<tags 
	 ms.service="cdn"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="mazha"/>



#Come abilitare la rete per la distribuzione di contenuti (CDN) per Azure  

È possibile abilitare la rete CDN per l’origine tramite il portale di gestione di Azure. Il tipo di origine disponibile corrente include: app Web, archiviazione, Servizi Cloud. È inoltre possibile abilitare la rete CDN per l'endpoint di streaming di Servizi multimediali di Azure. Dopo aver abilitato un endpoint della rete CDN per l'origine, tutti gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN.

Si noti che ora è inoltre possibile creare un'origine personalizzata che non deve necessariamente essere Azure.

##Per creare nuovo endpoint della rete CDN  

1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2.	Nel pannello di navigazione fare clic su **Rete CDN**.
3.	Sulla barra multifunzione fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo**, selezionare **SERVIZI APP**, quindi **CDN** e infine **CREAZIONE RAPIDA**.
4.	Nell’elenco a discesa **TIPO DI ORIGINE** selezionare un tipo di origine dall’elenco dei tipi di origine disponibili.
	
	Verrà visualizzato l'elenco di URL di origine disponibili nell’elenco a discesa **URL DI ORIGINE**.
	

	![createnew][createnew]

	Se si seleziona **Origine personalizzata**, è possibile immettere un URL di origine personalizzata, che non dispone di un'origine di Azure.

	![customorigin][customorigin]

	>[AZURE.NOTE]Attualmente è supportato solo HTTP per l'origine ed è necessario utilizzare l'estensione Servizi multimediali per abilitare la rete CDN di Azure per un endpoint di streaming di Azure Media Services.
	
5.	Per creare il nuovo endpoint, fare clic sul pulsante **Crea**.


>[AZURE.NOTE]la configurazione creata per l'endpoint non sarà disponibile immediatamente. Per la propagazione della registrazione nella rete CDN potrebbero essere necessari fino a 60 minuti. È possibile che gli utenti che provano a usare immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 400 (Richiesta non valida) fino a quando il contenuto non risulterà disponibile tramite la rete CDN.

##Vedere anche
[Come eseguire il mapping del contenuto della rete per la distribuzione di contenuti (CDN) a un dominio personalizzato](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=September15_HO1-->