<properties 
	pageTitle="Utilizzare le emoticon Emoji all'interno di Mobile Engagement di Azure" 
	description="Informazioni su come usare emoticon Emoji nelle notifiche push"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />  

#Usare emoticon Emoji nelle notifiche push

È possibile includere emoticon Emoji nelle notifiche push con pochi e semplici passaggi:

1. In primo luogo è necessario trovare l’Emoji che si desidera inviare nel messaggio. Assicurarsi che l’Emoji che si sta selezionando verrà supportata dal dispositivo di destinazione poiché i produttori del dispositivo impiegano del tempo per aggiungere gli Emojis appena approvati alle piattaforme del dispositivo.

2. Su **Windows** -è possibile passare a questo [collegamento](http://apps.timwhitlock.info/emoji/tables/unicode) e copiare l'icona 'Native'.

	![][7]

3. Su **Mac** -è possibile trovare gli Emojis nell'applicazione Dizionario, scegliendo Modifica -> Emoji e simboli.

	![][6]

4. A questo punto, passare alla scheda **Copertura** del portale di Azure Mobile Engagement. Selezionare il tipo di notifica push, ovvero annuncio, sondaggio e così via. Per questo esempio è stata scelta una notifica push di tipo annuncio.

5. Specificare i diversi campi della notifica fino a raggiungere il testo della notifica, in cui verrà aggiunto l'emoticon Emoji. È possibile scegliere di inserirlo nel titolo, nel messaggio o in entrambi. È necessario trascinare o copiare gli Emoji individuati dai percorsi precedenti.

	![][1]

6. Completare gli altri campi per la notifica e salvarla.

7. Quando si esegue un test o si attiva l'annuncio, verrà visualizzata una notifica con l'emoticon, in base a quanto specificato.

	![][3] ![][4] ![][5]

<!-- Images. -->  
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

<!---HONumber=AcomDC_0824_2016-->