---
title: Rotazione del certificato per il database SQL di Azure & SQL Istanza gestita
description: Informazioni sulle modifiche imminenti delle modifiche ai certificati radice che influiranno sul database SQL di Azure e su Azure SQL Istanza gestita
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: b0a10744d2b48fa620b48b731144222199f711c7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792532"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Informazioni sulle modifiche apportate alla modifica della CA radice per il database SQL di Azure & SQL Istanza gestita

Il database SQL di Azure & SQL Istanza gestita modificherà il certificato radice per l'applicazione client o il driver abilitato con SSL, usato per stabilire una connessione TDS sicura. Il [certificato radice corrente](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) è impostato in modo da scadere il 26 ottobre 2020 come parte delle procedure consigliate per la manutenzione e la sicurezza standard. In questo articolo vengono fornite ulteriori informazioni sulle modifiche imminenti, sulle risorse interessate e sui passaggi necessari per garantire che l'applicazione mantenga la connettività al server di database.

## <a name="what-update-is-going-to-happen"></a>Quale aggiornamento verrà eseguita?

[Forum del browser dell'autorità di certificazione (CA)](https://cabforum.org/) ha pubblicato di recente report di più certificati rilasciati dai fornitori di CA come non conformi.

In base ai requisiti di conformità del settore, i fornitori di CA hanno iniziato a revocare i certificati della CA per le CA non conformi, che richiedono che i server usino certificati rilasciati da CA conformi e firmati da certificati della CA da tali CA conformi. Poiché il database SQL di Azure & SQL Istanza gestita attualmente usa uno di questi certificati non conformi, che le applicazioni client usano per convalidare le connessioni SSL, è necessario assicurarsi che vengano eseguite le azioni appropriate (descritte di seguito) per ridurre al minimo il potenziale impatto sui server SQL di Azure.

Il nuovo certificato verrà usato a partire dal 26 ottobre 2020. Se si usa la convalida completa del certificato del server per la connessione da un client SQL (TrustServerCertificate = true), è necessario assicurarsi che il client SQL sia in grado di convalidare il nuovo certificato radice prima del 26 ottobre 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Ricerca per categorie verificare se l'applicazione potrebbe essere interessata?

Tutte le applicazioni che usano SSL/TLS e verificano che il certificato radice debba aggiornare il certificato radice per connettersi al database SQL di Azure & SQL Istanza gestita. 

Se non si usa SSL/TLS attualmente, non vi sono effetti sulla disponibilità dell'applicazione. È possibile verificare se l'applicazione client sta provando a verificare il certificato radice osservando la stringa di connessione. Se TrustServerCertificate è impostato in modo esplicito su true, non si è interessati.

Se il driver client usa l'archivio certificati del sistema operativo, la maggior parte dei driver esegue e il sistema operativo viene regolarmente gestito, questa modifica probabilmente non influirà sull'utente, perché il certificato radice a cui si passa dovrebbe essere già disponibile nell'archivio certificati radice attendibili. Verificare la presenza di Baltimore CyberDigiCert GlobalRoot G2 e verificare che sia presente.

Se il driver client usa l'archivio certificati del file locale, per evitare che la disponibilità dell'applicazione venga interrotta a causa di un arresto imprevisto dei certificati oppure per aggiornare un certificato revocato, vedere la sezione [**cosa devo fare per mantenere la connettività**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Cosa è necessario fare per mantenere la connettività

Per evitare che la disponibilità dell'applicazione venga interrotta a causa di un arresto imprevisto dei certificati o per aggiornare un certificato revocato, attenersi alla procedura seguente:

*   Scaricare Baltimore CyberTrust Root & DigiCert GlobalRoot G2 root CA dai collegamenti seguenti:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Sono inclusi un archivio certificati CA combinato con certificati **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** .

## <a name="what-can-be-the-impact"></a>Quale può essere l'effetto?
Se si convalidano i certificati del server come descritto in questo articolo, è possibile che la disponibilità dell'applicazione venga interrotta perché il database non sarà raggiungibile. A seconda dell'applicazione, è possibile ricevere una serie di messaggi di errore, tra cui:
*   Certificato/certificato revocato non valido
*   Timeout della connessione
*   Errore se applicabile

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Se non si usa SSL/TLS, è ancora necessario aggiornare la CA radice?
Se non si usa SSL/TLS, non sono necessarie azioni relative a questa modifica. È comunque necessario impostare un piano per iniziare a usare la versione più recente di TLS quando si prevede l'imposizione di TLS nel prossimo futuro.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Cosa accade se non si aggiorna il certificato radice prima del 26 ottobre 2020?
Se non si aggiorna il certificato radice prima del 30 novembre 2020, le applicazioni che si connettono tramite SSL/TLS e la verifica del certificato radice non saranno in grado di comunicare con il database SQL di Azure & SQL Istanza gestita e l'applicazione verificheranno problemi di connettività al database SQL di Azure & Istanza gestita SQL.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>È necessario pianificare un tempo di inattività di manutenzione per questa modifica?<BR>
No. Poiché la modifica qui è solo sul lato client per la connessione al server, per questa modifica non è necessario alcun tempo di inattività di manutenzione.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Cosa accade se non si riesce a ottenere un tempo di inattività pianificato per questa modifica prima del 26 ottobre 2020?
Poiché i client usati per la connessione al server devono aggiornare le informazioni del certificato come descritto [nella sezione correzione](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity), in questo caso non è necessario un tempo di inattività per il server.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Se si crea un nuovo server dopo il 30 novembre 2020, I ripercussioni saranno compromessi?
Per i server creati dopo il 26 ottobre 2020, è possibile usare il certificato appena emesso per le applicazioni per la connessione tramite SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Con quale frequenza Microsoft aggiorna i certificati o quali sono i criteri di scadenza?
Questi certificati usati dal database SQL di Azure & SQL Istanza gestita sono forniti da autorità di certificazione (CA) attendibili. Il supporto di questi certificati nel database SQL di Azure & Istanza gestita SQL è quindi associato al supporto di questi certificati da parte della CA. Tuttavia, come in questo caso, in questi certificati predefiniti possono essere presenti bug non previsti, che devono essere corretti al più presto.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>Se si usano le repliche di lettura, è necessario eseguire questo aggiornamento solo sul server master o su tutte le repliche di lettura?
Poiché questo aggiornamento è una modifica sul lato client, se il client utilizzato per leggere i dati dal server di replica, sarà necessario applicare anche le modifiche per tali client. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>È presente una query sul lato server per verificare se è in uso SSL?
Poiché questa configurazione è sul lato client, le informazioni non sono disponibili sul lato server.

### <a name="what-if-i-have-further-questions"></a>Che cosa succede se ho altre domande?
Se si ha un piano di supporto ed è necessaria assistenza tecnica, creare una richiesta di supporto di Azure, vedere [come creare una richiesta di supporto di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).