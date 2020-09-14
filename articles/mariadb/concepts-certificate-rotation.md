---
title: Rotazione dei certificati per database di Azure per MariaDB
description: Informazioni sulle modifiche imminenti delle modifiche apportate al certificato radice che influiranno sul database di Azure per MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: ba5a1a0eb61ca086c3cd1ea66acddc18e950871d
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057250"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Informazioni sulle modifiche apportate alla modifica della CA radice per il database di Azure per MariaDB

Il database di Azure per MariaDB modificherà il certificato radice per l'applicazione client o il driver abilitato con SSL, da usare per [connettersi al server di database](concepts-connectivity-architecture.md). Il certificato radice attualmente disponibile è impostato sulla scadenza del 26 ottobre 2020 (10/26/2020) come parte delle procedure consigliate per la manutenzione e la protezione standard. In questo articolo vengono fornite ulteriori informazioni sulle modifiche imminenti, sulle risorse interessate e sui passaggi necessari per garantire che l'applicazione mantenga la connettività al server di database.

## <a name="what-update-is-going-to-happen"></a>Quale aggiornamento verrà eseguita?

In alcuni casi, le applicazioni utilizzano un file di certificato locale generato da un file di certificato dell'autorità di certificazione (CA) attendibile per la connessione sicura. Attualmente i clienti possono usare solo il certificato predefinito per connettersi a un database di Azure per il server MariaDB, disponibile [qui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Tuttavia, il [Forum del browser dell'autorità di certificazione (CA)](https://cabforum.org/)ha pubblicato di   recente report di più certificati rilasciati dai fornitori di CA come non conformi.

In base ai requisiti di conformità del settore, i fornitori di CA hanno iniziato a revocare i certificati della CA per le CA non conformi, che richiedono che i server usino certificati rilasciati da CA conformi e firmati da certificati della CA da tali CA conformi. Poiché il database di Azure per MariaDB attualmente usa uno di questi certificati non conformi, che le applicazioni client usano per convalidare le connessioni SSL, è necessario assicurarsi che vengano eseguite le azioni appropriate (descritte di seguito) per ridurre al minimo il potenziale impatto sui server MariaDB.


Il nuovo certificato verrà usato a partire dal 26 ottobre 2020 (10/26/2020). Se si usa la convalida della CA o la convalida completa del certificato del server quando ci si connette da un client MySQL (sslmode = Verify-CA o sslmode = Verify-Full), è necessario aggiornare la configurazione dell'applicazione prima del 26 ottobre 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Ricerca per categorie verificare se il database sarà interessato?

Tutte le applicazioni che usano SSL/TLS e verificano che il certificato radice debba aggiornare il certificato radice per connettersi al database di Azure per MariaDB. Se non si usa SSL/TLS attualmente, non vi sono effetti sulla disponibilità dell'applicazione. È possibile verificare se l'applicazione client sta provando a usare la modalità SSL con l'autorità di certificazione attendibile (CA) predefinita [qui](concepts-ssl-connection-security.md#default-settings).

Per evitare che la disponibilità dell'applicazione venga interrotta a causa di un arresto imprevisto dei certificati o per aggiornare un certificato revocato, vedere la sezione [**"cosa devo fare per mantenere la connettività"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Cosa è necessario fare per mantenere la connettività

Per evitare che la disponibilità dell'applicazione venga interrotta a causa di un arresto imprevisto dei certificati o per aggiornare un certificato revocato, attenersi alla procedura seguente:

*   Scaricare la CA **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** dai collegamenti seguenti:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Sono inclusi un archivio certificati CA combinato con certificati **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** .
    *   Per gli utenti Java (MariaDB Connector/J), eseguire:

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Sostituire quindi il file dell'archivio chiavi originale con quello nuovo generato:
        *   System. SetProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file"); 
        *   System. SetProperty ("javax. NET. SSL. trustStorePassword", "password");
    *   Per gli utenti .NET (MariaDB Connector/NET, MariaDBConnector), verificare che **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** siano presenti nell'archivio certificati di Windows, in autorità di certificazione radice attendibili. Se non esistono certificati, importare il certificato mancante.

        ![Certificato .NET per database di Azure per MariaDB](media/overview/netconnecter-cert.png)

    *   Per gli utenti .NET in Linux che usano SSL_CERT_DIR, assicurarsi che **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** siano presenti nella directory indicata da SSL_CERT_DIR. Se non esistono certificati, creare il file del certificato mancante.

    *   Per altri utenti (MariaDB client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), è possibile unire due file di certificato CA come questo formato</b>

        </br>-----INIZIO CERTIFICATO-----
 </br>(Radice CA1: BaltimoreCyberTrustRoot. CRT. PEM)
 </br>-----CERTIFICATO-----END
 </br>-----INIZIO CERTIFICATO-----
 </br>(CA2 radice: DigiCertGlobalRootG2. CRT. PEM)
 </br>-----CERTIFICATO-----END

*   Sostituire il file PEM CA radice originale con il file CA radice combinato e riavviare l'applicazione/client.
*   In futuro, dopo la distribuzione del nuovo certificato sul lato server, è possibile modificare il file di CA PEM in DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact"></a>Quale può essere l'effetto?
Se si usa il certificato rilasciato da database di Azure per MariaDB, come descritto in questo articolo, la disponibilità dell'applicazione potrebbe essere interrotta perché il database non sarà raggiungibile. A seconda dell'applicazione, è possibile ricevere una serie di messaggi di errore, tra cui:
*   Certificato/certificato revocato non valido
*   Timeout della connessione
*   Errore se applicabile

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. se non si usa SSL/TLS, è ancora necessario aggiornare la CA radice?
Non sono necessarie azioni se non si usa SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Se si usa SSL/TLS, è necessario riavviare il server di database per aggiornare la CA radice?
No, non è necessario riavviare il server di database per iniziare a usare il nuovo certificato. Si tratta di una modifica lato client e le connessioni client in ingresso devono usare il nuovo certificato per assicurarsi che possano connettersi al server di database.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. cosa accade se non si aggiorna il certificato radice prima del 26 ottobre 2020 (10/26/2020)?
Se non si aggiorna il certificato radice prima del 30 novembre 2020, le applicazioni che si connettono tramite SSL/TLS e la verifica per il certificato radice non saranno in grado di comunicare con il server di database MariaDB e l'applicazione verificherà problemi di connettività al server di database MariaDB.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. è necessario pianificare un tempo di inattività di manutenzione per questa modifica?<BR>
No. Poiché la modifica è solo sul lato client per la connessione al server di database, per questa modifica non è necessario alcun tempo di inattività di manutenzione.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. cosa accade se non è possibile ottenere un tempo di inattività pianificato per questa modifica prima del 26 ottobre 2020 (10/26/2020)?
Poiché i client usati per la connessione al server devono aggiornare le informazioni del certificato come descritto [nella sezione correzione](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), in questo caso non è necessario un tempo di inattività per il server.

###  <a name="6-if-i-create-a-new-server-after-nov-30th-will-i-be-impacted"></a>6. Se si crea un nuovo server dopo il 30 novembre, I ripercussioni saranno compromessi?
Per i server creati dopo il 26 ottobre 2020 (10/26/2020), è possibile usare il certificato appena emesso per le applicazioni per la connessione tramite SSL.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. con quale frequenza Microsoft aggiorna i certificati o quali sono i criteri di scadenza?
Questi certificati usati da database di Azure per MariaDB sono forniti da autorità di certificazione (CA) attendibili. Il supporto di questi certificati nel database di Azure per MariaDB è quindi associato al supporto di questi certificati da parte della CA. Tuttavia, come in questo caso, in questi certificati predefiniti possono essere presenti bug non previsti, che devono essere corretti al più presto.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. Se si usano le repliche di lettura, è necessario eseguire questo aggiornamento solo sul server master o su tutte le repliche di lettura?
Poiché questo aggiornamento è una modifica sul lato client, se il client utilizzato per leggere i dati dal server di replica, sarà necessario applicare anche le modifiche per tali client. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. è presente una query sul lato server per verificare se è in uso SSL?
Per verificare se si sta usando la connessione SSL per la connessione al server, fare riferimento alla [verifica SSL](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="10-what-if-i-have-further-questions"></a>10. cosa accade se si hanno altre domande?
In caso di domande, ottenere le risposte dagli esperti della community in [Microsoft Q&a](mailto:AzureDatabaseformariadb@service.microsoft.com). Se si dispone di un piano di supporto ed è necessario assistenza tecnica, [contattare](mailto:AzureDatabaseformariadb@service.microsoft.com) Microsoft
