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
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#Come abilitare la rete per la distribuzione di contenuti (CDN) per Azure  

Dopo aver abilitato un endpoint della rete CDN per l'origine, ad esempio un account di archiviazione o un servizio cloud, tutti gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN.  


##Per creare nuovo endpoint della rete CDN  

1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2.	Nel pannello di navigazione fare clic su **Rete CDN**.
3.	Sulla barra multifunzione fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Servizi app**, quindi **CDN** e infine **Creazione rapida**.
4.	Nell'elenco a discesa **Dominio di origine** selezionare la destinazione desiderata nell'elenco dei tipi di origine disponibili.
5.	Per creare il nuovo endpoint, fare clic sul pulsante **Crea**.




> Nota: la configurazione creata per l'endpoint non sarà disponibile immediatamente. Per la propagazione della registrazione nella rete CDN potrebbero essere necessari fino a 60 minuti. È possibile che gli utenti che provano a usare immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 400 (Richiesta non valida) fino a quando il contenuto non risulterà disponibile tramite la rete CDN.

#Vedere anche
[Come eseguire il mapping del contenuto della rete per la distribuzione di contenuti (CDN) a un dominio personalizzato](./cdn-map-content-to-custom-domain.md)

<!--HONumber=49-->