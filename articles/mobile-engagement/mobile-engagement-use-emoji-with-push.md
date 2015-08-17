<properties 
	pageTitle="Usare emoticon Emoji nelle notifiche push" 
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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#Usare emoticon Emoji nelle notifiche push

È possibile includere emoticon Emoji nelle notifiche push. Azure Mobile Engagement supporta attualmente solo il set di emoticon Emoji a 3 byte per notifiche di testo in-app ed esterne all'app. Eseguire i passaggi seguenti:

1.  È prima di tutto necessario trovare una libreria di emoticon Emoji a 3 byte. Tutti gli emoticon Emoji che possono essere usati sono disponibili nel [collegamento](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not) seguente.

	![][1]

2. Passare alla scheda Copertura del portale di Azure Mobile Engagement.

3. Selezionare il tipo di notifica push, ovvero annuncio, sondaggio e così via. Per questo esempio è stata scelta una notifica push di tipo annuncio.

4. Specificare i diversi campi della notifica fino a raggiungere il testo della notifica, in cui verrà aggiunto l'emoticon Emoji. È possibile scegliere di inserirlo nel titolo, nel messaggio o in entrambi.

	![][2]

5. Tagliare l'emoticon Emoji da usare dal collegamento precedente. Incollarlo direttamente nel titolo e/o nel messaggio, nella posizione scelta.

6. Completare gli altri campi per la notifica e salvarla.

7. Quando si esegue un test o si attiva l'annuncio, verrà visualizzata una notifica con l'emoticon, in base a quanto specificato.

	![][3] 
	![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!---HONumber=August15_HO6-->