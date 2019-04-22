---
title: Supporto di Java Enterprise in Linux - Servizio app di Azure | Microsoft Docs
description: Guida per sviluppatori sulla distribuzione di app Java Enterprise usando Wildfly con il Servizio app di Azure in Linux.
keywords: servizio app di Azure, app web, linux, oss, java, wildfly, enterprise, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 8db65fd9a1f271aea4ceb345f4d9dfbb6b9ff8a6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877381"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Guida di Java Enterprise per il Servizio app di Azure in Linux

> [!NOTE] 
> Java Enterprise Edition in servizio App Linux è attualmente in anteprima. Lo stack è **non** consigliato per la produzione rivolte al lavoro. Vedere le [manuale dello sviluppatore Java](app-service-linux-java.md) per informazioni sul nostro stack Java SE e Tomcat.

Servizio App di Azure in Linux consente agli sviluppatori Java per creare, distribuire e scalare le applicazioni aziendali Java (EE Java) in un servizio completamente gestito basato su Linux.  L'ambiente di runtime Java Enterprise sottostante è il server applicazioni open source [Wildfly](https://wildfly.org/).

Questa guida illustra i concetti chiave e le istruzioni per gli sviluppatori Java Enterprise che usano il Servizio app di Azure per Linux. Se non si ha familiarità con lo sviluppo di applicazioni Java con il Servizio app di Azure per Linux, è consigliabile completare prima di tutto la [guida introduttiva per Java](quickstart-java.md). Le eventuali domande sul Servizio app di Azure per Linux che non riguardano Java Enterprise nello specifico trovano risposta nella [guida per sviluppatori di Java](app-service-linux-java.md) e nelle [domande frequenti sul Servizio app di Azure per Linux](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Ridimensionare con il Servizio app di Azure 

Il server applicazioni WildFly del Servizio app di Azure per Linux viene eseguito in modalità autonoma, non in una configurazione di dominio. Quando si amplia il piano di Servizio app, ogni istanza di WildFly viene configurata come server autonomo.

 Per scalare l'applicazione orizzontalmente o verticalmente, si usano le [regole di scalabilità](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) e si [aumenta il numero di istanze](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). 

## <a name="customize-application-server-configuration"></a>Personalizzare la configurazione del server applicazioni

Le istanze dell'app Web sono senza stato, quindi ogni nuova istanza avviata deve essere configurata all'avvio per supportare la configurazione Wildfly richiesta dall'applicazione.
È possibile scrivere uno script Bash di avvio per chiamare l'interfaccia della riga di comando di WildFly per:

- Configurare le origini dati
- Configurare i provider di messaggistica
- Aggiungere altri moduli e altre dipendenze alla configurazione del server Wildfly.

  Lo script viene eseguito quando Wildfly è in esecuzione, ma prima dell'avvio dell'applicazione. È consigliabile che lo script usi l'[interfaccia della riga di comando di JBOSS](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) chiamata da `/opt/jboss/wildfly/bin/jboss-cli.sh` per configurare il server applicazioni con qualsiasi configurazione o modifica necessaria dopo l'avvio del server. 

Non usare la modalità interattiva dell'interfaccia della riga di comando per configurare Wildfly. Si può invece specificare uno script di comandi all'interfaccia della riga di comando di JBoss usando il comando `--file`, ad esempio:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Caricare lo script di avvio in `/home/site/deployments/tools` nell'istanza del Servizio app di Azure. Per le istruzioni su come ottenere le credenziali FTP, vedere [questo documento](/azure/app-service/deploy-configure-credentials#userscope). 

Impostare il campo **Script di avvio** nel portale di Azure sul percorso dello script della shell di avvio, ad esempio `/home/site/deployments/tools/your-startup-script.sh`.

Specificare le [impostazioni dell'applicazione](/azure/app-service/web-sites-configure#application-settings) nella configurazione dell'applicazione per passare le variabili di ambiente da usare nello script. Le impostazioni dell'applicazione mantengono le stringhe di connessione e altri segreti necessari per configurare il controllo delle versioni dell'applicazione.

## <a name="modules-and-dependencies"></a>Moduli e dipendenze

Per installare i moduli e le relative dipendenze nella classpath di Wildfly tramite l'interfaccia della riga di comando di JBoss, sarà necessario creare i file seguenti nella directory specifica.  Per alcuni moduli e dipendenze può essere necessario specificare una configurazione aggiuntiva, ad esempio la denominazione JNDI o un'altra configurazione specifica dell'API. L'elenco che segue include gli elementi minimi necessari per configurare una dipendenza nella maggior parte dei casi.

- Un [descrittore di modulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Questo file XML definisce il nome, gli attributi e le dipendenze del modulo. Questo [file module.xml di esempio](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definisce un modulo Postgres, la relativa dipendenza JDBC del file JAR e altre dipendenze del modulo richieste.
- Le dipendenze del file con estensione JAR necessarie per il modulo.
- Uno script con i comandi dell'interfaccia della riga di comando di JBoss per configurare il nuovo modulo. Questo file conterrà i comandi che si indica all'interfaccia della riga di comando di JBoss di eseguire per configurare il server per usare la dipendenza. Per la documentazione sui comandi per aggiungere moduli, origini dati e provider di messaggistica, fare riferimento a [questo documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  Uno script Bash di avvio per chiamare l'interfaccia della riga di comando di JBoss ed eseguire lo script del passaggio precedente. Questo file viene eseguito quando l'istanza del Servizio app di Azure viene riavviata o quando viene effettuato il provisioning di nuove istanze durante un'operazione di scale-out.  Questo script di avvio è usato per eseguire qualsiasi altra configurazione per l'applicazione man mano che i comandi di JBoss vengono passati all'interfaccia della riga di comando di JBoss. Questo file può essere come minimo un singolo comando per passare lo script di comandi della CLI di JBoss all'interfaccia della riga di comando di JBoss: 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

Dopo avere creato il file e il contenuto per il modulo, seguire la procedura sottostante per aggiungere il modulo al server applicazioni Wildfly. 

1. Trasferire tramite FTP i file in `/home/site/deployments/tools` nell'istanza del Servizio app di Azure. Per le istruzioni su come ottenere le credenziali FTP, vedere questo documento. 
2. Nel pannello Impostazioni applicazione del portale di Azure impostare il campo "Script di avvio" sul percorso dello script della shell di avvio, ad esempio `/home/site/deployments/tools/your-startup-script.sh`.
3. Riavviare l'istanza del Servizio app di Azure selezionando il pulsante **Riavvia** nella sezione **Panoramica** del portale di Azure o usando l'interfaccia della riga di comando di Azure.

## <a name="data-sources"></a>Origini dati

Per configurare in Wildfly una connessione all'origine dati, seguire lo stesso processo descritto nella sezione precedente "Moduli e dipendenze". È possibile seguire gli stessi passaggi per qualsiasi servizio di database di Azure.

1. Scaricare il driver JDBC per la versione del database. Per praticità, ecco i driver per [Postgres](https://jdbc.postgresql.org/download.html) e [MySQL](https://dev.mysql.com/downloads/connector/j/). Decomprimere il pacchetto scaricato per ottenere il file con estensione jar.
2. Seguire i passaggi descritti in "Moduli e dipendenze" per creare e caricare il descrittore di modulo XML, lo script dell'interfaccia della riga di comando di JBoss, lo script di avvio e il file della dipendenza con estensione jar di JDBC.


Sono disponibili altre informazioni sulla configurazione di Wildfly con [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) e [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898). È possibile usare queste istruzioni personalizzate con l'approccio generalizzato sopra indicato per aggiungere definizioni di origini dati al server.

## <a name="messaging-providers"></a>Provider di messaggistica

Per abilitare Beans basato su messaggi usando il bus di servizio come meccanismo di messaggistica:

1. Usare la [libreria di messaggistica di Apache QPId JMS](https://qpid.apache.org/proton/index.html). Includere questa dipendenza nel file pom.xml (o in un altro file di compilazione) per l'applicazione.

2.  Creare le [risorse del bus di servizio](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Creare uno spazio dei nomi del bus di servizio di Azure, una coda all'interno di tale spazio dei nomi e criteri di accesso condiviso con capacità di invio e di ricezione.

3. Passare la chiave dei criteri di accesso condiviso al codice tramite la codifica URL della chiave primaria dei criteri o [usare il Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Seguire i passaggi descritti nella sezione "Moduli e dipendenze" con il descrittore del modulo XML, le dipendenze con estensione jar, i comandi dell'interfaccia della riga di comando di JBoss e lo script di avvio per il provider JMS. Oltre ai quattro file, sarà necessario creare un file XML che definisce il nome JNDI per l'argomento e la coda JMS. Vedere [questo repository](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) per i file di configurazione di riferimento.


## <a name="configure-session-management-caching"></a>Configurare la cache per la gestione delle sessioni

Per impostazione predefinita, il Servizio app di Azure per Linux userà i cookie di affinità di sessione per garantire che le richieste dei client con sessioni esistenti vengano instradate alla stessa istanza dell'applicazione. Questo comportamento predefinito non richiede alcuna configurazione, ma presenta alcune limitazioni:

- Se un'istanza dell'applicazione viene riavviata o ridotta, lo stato della sessione utente nel server di applicazioni verrà perso.
- Se le applicazioni sono impostate su un timeout della sessione prolungato o un numero fisso di utenti, la ricezione del carico da parte delle nuove istanze scalate automaticamente può richiedere tempo poiché solo le nuove sessioni verranno instradate verso le istanze appena avviate.

È possibile configurare Wildfly in modo che usi un archivio delle sessioni esterne, ad esempio la [Cache Redis di Azure](/azure/azure-cache-for-redis/). Sarà necessario [disabilitare la configurazione di affinità di istanza ARR esistente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per disattivare l'instradamento basato sui cookie della sessione e consentire a un archivio delle sessioni di Wildfly di operare senza interferenze.

## <a name="enable-web-sockets"></a>Abilitare i WebSocket

Per impostazione predefinita, i WebSocket sono abilitati nel Servizio app di Azure. Per iniziare a usare i WebSocket nell'applicazione, fare riferimento a [questa Guida introduttiva](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Log e risoluzione dei problemi

Il Servizio app di Azure include strumenti che aiutano a risolvere i problemi con l'applicazione di interesse.

-   Abilitare la registrazione facendo clic su **Log di diagnostica** nel riquadro di spostamento a sinistra. Fare clic su **File System** per impostare il periodo di conservazione e la quota di archiviazione, quindi salvare le modifiche. È possibile trovare questi log in `/home/LogFiles/`.
-   [Usare il protocollo SSH per connettersi all'istanza dell'applicazione](app-service-linux-ssh-support.md) per visualizzare i log relativi all'applicazione in esecuzione.
-   Controllare i log di diagnostica nel pannello **Log di diagnostica** del portale di Azure o usando il comando dell'interfaccia della riga di comando di Azure: `az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group>`
