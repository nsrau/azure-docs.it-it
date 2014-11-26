<properties linkid="develop-php-tutorials-convert-wordpress-to-multisite" urlDisplayName="Convert a WordPress Site to a Multisite" pageTitle="Convert a WordPress Site to a Multisite" metaKeywords="WordPress, Multisite" description="Learn how to take an existing WordPress website created through the gallery in Azure and convert it to WordPress Multisite" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Convert a WordPress Site to a Multisite" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Conversione di un sito WordPress in un multisito

*Autore: [Ben Lobaugh][Ben Lobaugh], [Microsoft Open Technologies Inc.][Microsoft Open Technologies Inc.]*

In questa esercitazione si apprenderà come accedere a un sito Web WordPress creato tramite la raccolta in Azure e convertirlo in un'installazione multisito WordPress. Si apprenderà inoltre come assegnare un dominio personalizzato a ogni sito secondario all'interno dell'installazione.

Si presuppone che sia già disponibile un'installazione esistente di WordPress. In caso contrario, attenersi alle linee guida fornite in [Creazione di un sito Web WordPress dalla raccolta in Azure][Creazione di un sito Web WordPress dalla raccolta in Azure].

La conversione di un'installazione per singolo sito WordPress esistente in una multisito è in genere un'operazione abbastanza semplice. Alcuni dei passaggi iniziali sono in pratica identici a quelli della pagina relativa alla [creazione di una rete][creazione di una rete] di [WordPress Codex][WordPress Codex].

Di seguito verrà descritta la procedura dettagliata.

## Abilitare il multisito

È necessario prima abilitare il multisito tramite il file `wp-config.php` con la costante **WP\_ALLOW\_MULTISITE**. È possibile modificare i file del sito Web in due modi diversi: tramite FTP oppure tramite Git. Se non si è esperti di configurazione tramite FTP o Git, fare riferimento alle esercitazioni seguenti:

-   [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP][Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP]

-   [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git][Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git]

Aprire il file `wp-config.php` con l'editor preferito e aggiungere quanto segue sopra la riga `/* That's all, stop editing! Happy blogging. */`.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Salvare il file e caricarlo di nuovo nel server.

## Configurazione della rete

Accedere all'area *wp-admin* del sito Web. Si noterà un nuovo elemento **Network Setup** nel menu **Tools**. Fare clic su **Network Setup** e inserire i dettagli relativi alla rete.

![Schermata di configurazione della rete][Schermata di configurazione della rete]

In questa esercitazione viene utilizzata lo schema di sito *Sub-directories* che dovrebbe essere sempre funzionante. I domini personalizzati per ciascun sito secondario verranno configurati più avanti in questa esercitazione. Dovrebbe tuttavia essere possibile configurare un'installazione di sottodominio se si mappa un dominio tramite il portale e si configura correttamente il DNS con caratteri jolly.

Per altre informazioni sulla differenza tra le configurazioni di sottodomini e sottodirectory, vedere l'articolo relativo ai [tipi di rete multisito][tipi di rete multisito] su WordPress Codex.

## Abilitare la rete

La rete è stata configurata nel database, ma per abilitare la funzionalità di rete è necessario eseguire un ulteriore passaggio. Finalizzare le impostazioni di `wp-config.php` e verificare che `web.config` reindirizzi in modo corretto ciascun sito.

Dopo aver fatto clic sul pulsante **Installa** nella pagina *Configurazione della rete*, WordPress proverà ad aggiornare i file `wp-config.php` e `web.config`. È tuttavia consigliabile controllare sempre i file per verificare la riuscita degli aggiornamenti. In caso contrario, in questa schermata verranno visualizzati gli aggiornamenti necessari. Modificare e salvare i file.

Dopo aver effettuato gli aggiornamenti, sarà necessario disconnettersi e riconnettersi al dashboard wp-admin.

Sulla barra di amministrazione dovrebbe ora essere presente un ulteriore menu denominato **My Sites**. Tale menu consente di controllare la nuova rete tramite il dashboard **Network Admin**.

# Aggiunta di domini personalizzati

Con il plug-in [WordPress MU Domain Mapping][WordPress MU Domain Mapping] è semplicissimo aggiungere domini personalizzati a qualsiasi sito della rete. Per il corretto funzionamento del plug-in è necessario eseguire alcune operazioni aggiuntive di configurazione sul portale, nonché presso il registrar.

## Abilitare il mapping di dominio con il sito Web

La modalità per siti Web gratuiti predefinita non supporta l'aggiunta di domini personalizzati ai siti Web di Azure. È quindi necessario passare alla modalità condivisa o standard. A tale scopo, effettuare l'operazione seguente:

-   Accedere al portale di Azure e trovare il sito Web.
-   Fare clic sulla scheda **Scale** nell'area del contenuto principale.
-   In **General** selezionare *SHARED* o *STANDARD*
-   Fare clic su **Save**.

È possibile che venga ricevuto un messaggio in cui si chiede di verificare la modifica e di accettare che, a seconda dell'uso e delle altre opzioni di configurazione impostate, il sito Web può ora comportare costi.

L'elaborazione delle nuove impostazioni richiede alcuni secondi, pertanto è consigliabile iniziare subito a configurare il dominio.

## Verifica del dominio

Per consentire a Siti Web di Azure di mappare un dominio al sito, è prima necessario verificare di disporre dell'autorizzazione per il mapping del dominio. A tale scopo, è necessario aggiungere un nuovo record CNAME alla voce DNS.

-   Accedere al gestore DNS del dominio.
-   Creare un nuovo CNAME *awverify*.
-   Impostare *awverify* in modo che punti a *awverify.DOMINIO.azurewebsites.net*

L'applicazione delle modifiche DNS può richiedere tempo, pertanto se i passaggi seguenti non funzionano immediatamente, attendere qualche minuto prima di riprovare.

## Aggiungere il dominio al sito Web

Tornare al sito Web tramite il portale di Azure e questa volta fare clic sulla scheda **CONFIGURA**. Il pulsante **GESTISCI DOMINI** dovrebbe essere disponibile, quindi farvi clic.

Verrà visualizzata la finestra di dialogo *Gestisci domini personalizzati*, in cui immettere tutti i domini da assegnare al sito Web. Se un dominio non è incluso nell'elenco, non sarà disponibile per il mapping in WordPress, indipendentemente dalla modalità di configurazione del DNS del dominio.

![Finestra di dialogo Manage custom domains][Finestra di dialogo Manage custom domains]

Dopo aver digitato il dominio nella casella di testo, Azure verificherà il record CNAME *awverify* creato in precedenza. Se il DNS non è stato completamente propagato, verrà visualizzato un indicatore rosso. Se invece l'operazione è riuscita, verrà visualizzato un segno di spunta verde.

Prendere nota dell'indirizzo IP indicato nella parte inferiore della finestra di dialogo in quanto sarà necessario per configurare il record A per il dominio.

## Configurare il record A del dominio

Se gli altri passaggi sono stati completati correttamente, è ora possibile assegnare il dominio al sito Web di Azure tramite un record A DNS.

È importante notare che Siti Web di Azure accetta sia record CNAME che A, tuttavia è *necessario* usare un record A per abilitare il mapping del dominio corretto. Un CNAME non può essere inoltrato a un altro CNAME, ovvero a quello creato da Azure con DOMINIO.azurewebsites.net.

Se si usa l'indirizzo IP del passaggio precedente, tornare al gestore DNS e configurare il record A in modo che punti a tale IP.

## Installare e configurare il plug-in

La modalità multisito di WordPress non include al momento un metodo incorporato per mappare i domini personalizzati. Esiste tuttavia un plug-in denominato [WordPress MU Domain Mapping][WordPress MU Domain Mapping] che consente di aggiungere tale funzionalità. Accedere alla parte Network Admin del sito e installare il plug-in **WordPress MU Domain Mapping**.

Dopo aver installato e attivato il plug-in, fare clic su **Settings** \> **Domain Mapping** per configurarlo. Nella prima casella di testo *Server IP Address* immettere l'indirizzo IP utilizzato per configurare il record A per il dominio. Impostare le eventuali *opzioni di dominio* desiderate (anche se le impostazioni predefinite sono spesso adeguate), quindi fare clic su **Save**.

## Mappare il dominio

Visualizzare il **Dashboard** relativo al sito a cui mappare il dominio. Fare clic su **Tools** \> **Domain Mapping** e digitare il nuovo dominio nella casella di testo, quindi fare clic su **Add**.

Per impostazione predefinita, il nuovo dominio verrà riscritto nel dominio del sito generato automaticamente. Se si desidera che tutto il traffico venga inviato al nuovo dominio, selezionare la casella *Primary domain for this blog* prima di salvare. È possibile aggiungere a un sito un numero illimitato di domini, tuttavia solo uno può essere quello primario.

## Ripetere l'operazione

Siti Web di Azure consente di aggiungere un numero illimitato di domini a un sito Web. Per aggiungere altri domini, sarà necessario eseguire per ciascun dominio le operazioni descritte nelle sezioni **Verificare il dominio** e **Configurare il record A del dominio**.

  [Ben Lobaugh]: http://ben.lobaugh.net
  [Microsoft Open Technologies Inc.]: http://msopentech.com
  [Creazione di un sito Web WordPress dalla raccolta in Azure]: https://www.windowsazure.com/it-it/develop/php/tutorials/website-from-gallery/
  [creazione di una rete]: http://codex.wordpress.org/Create_A_Network
  [WordPress Codex]: http://codex.wordpress.org
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP]: https://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git]: https://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-git/#header-1
  [Schermata di configurazione della rete]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
  [tipi di rete multisito]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
  [WordPress MU Domain Mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/
  [Finestra di dialogo Manage custom domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png
