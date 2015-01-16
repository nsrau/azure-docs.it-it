<properties urlDisplayName="How to create" pageTitle="Come creare siti Web - Gestione servizi di Azure" metaKeywords="Azure creating website, Azure deleting website" description="Informazioni su come creare un sito Web usando il portale di gestione di Azure." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Come creare un sito Web

Questo argomento descrive come creare un sito Web dalla raccolta oppure mediante il portale di gestione.

Per informazioni su come distribuire il contenuto in un sito Web di Azure creato, vedere la sezione **Distribuzione** in [Siti Web di Azure](/it-it/documentation/services/web-sites/).

## Sommario ##

- [Procedura: Creare un sito Web mediante il portale di gestione](#createawebsiteportal)
- [Procedura: Creare un sito Web dalla raccolta](#howtocreatefromgallery)
- [Procedura: Eliminare un sito Web](#deleteawebsite)
- [Passaggi successivi](#nextsteps)

##<a name="createawebsiteportal"></a>Procedura: Creare un sito Web mediante il portale di gestione

Per creare un sito Web in Azure, attenersi alla procedura seguente.
	
1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).

2. Fare clic sull'icona **Crea nuovo** in basso a sinistra nel portale di gestione.

3. Fare clic sull'icona **Sito Web** e quindi sull'icona **Creazione rapida**, immettere un valore per l'URL e selezionare il segno di spunta accanto a **Crea sito Web** nell'angolo in basso a destra della pagina.

4. Dopo la creazione del sito Web verrà visualizzato il messaggio **La creazione sito Web <*nome del sito Web*> è riuscita**. Per passare al sito Web, fare clic su **Sfoglia** nella parte inferiore della pagina del portale.

5. Nel portale fare clic sul nome del sito Web visualizzato nell'elenco dei siti Web per aprire la pagina di gestione **Avvio rapido** del sito.

6. Nella pagina **Avvio rapido** sono visualizzate le opzioni per ottenere gli strumenti di sviluppo per il sito Web, configurare la pubblicazione del sito o configurare la distribuzione da un provider del controllo del codice sorgente come TFS o Git. La pubblicazione con FTP è configurata per impostazione predefinita per i siti Web e il nome host FTP è visualizzato in **Nome host FTP** nella sezione **Riepilogo rapido** della pagina **Dashboard**. Prima della pubblicazione con FTP o Git, scegliere l'opzione **Reimposta credenziali di distribuzione** nella pagina **Dashboard** per poter eseguire l'autenticazione con l'host FTP o il repository Git durante la distribuzione di contenuto nel sito Web.

7. Nella pagina di gestione **Configura** sono visualizzate le impostazioni per il sito Web seguenti:

	- Versione di .NET Framework o PHP per l'applicazione Web
	- Associazioni SSL
	- Nomi di dominio personalizzati
	- Opzioni di registrazione
	- Impostazioni app per l'ambiente Azure (ad esempio eseguendo l'override di <appSettings> nel file Web.config dell'ambiente di sviluppo)
	- Stringhe di connessione (ad esempio eseguendo l'override di <connectionStrings> nel file Web.config dell'ambiente di sviluppo)
	- Processori script per estensioni file specifiche come *.php

##<a name="howtocreatefromgallery"></a>Procedura: Creare un sito Web dalla raccolta

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Procedura: Eliminare un sito Web
Per eliminare i siti Web usare l'icona **Elimina** del portale di gestione di Azure. L'icona **Elimina** è disponibile nel portale di Azure quando si fa clic su **Siti Web** per visualizzare l'elenco di tutti i siti Web, nonché nella parte inferiore di tutte le pagine di gestione del sito Web.

##<a name="nextsteps"></a>Passaggi successivi

Per altre informazioni, vedere [Siti Web di Azure](/it-it/documentation/services/web-sites/).

<!--HONumber=35.1-->
