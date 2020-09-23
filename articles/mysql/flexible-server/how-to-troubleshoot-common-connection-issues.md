---
title: Risolvere i problemi di connessione-database di Azure per MySQL-server flessibile
description: Informazioni su come risolvere i problemi di connessione al server flessibile di database di Azure per MySQL.
keywords: connessione MySQL, stringa di connessione, problemi di connettività, errore persistente, errore di connessione
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: b328da01cd3b2ecb3eb5f183296eab808de27399
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937278"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Risolvere i problemi di connessione al database di Azure per MySQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

I problemi di connessione possono avere varie cause tra cui:

* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso non corrette
* Limite massimo raggiunto in alcune risorse del server flessibili di database di Azure per MySQL

In questo articolo verrà illustrato come risolvere alcuni degli errori comuni e i passaggi per risolvere questi errori.

## <a name="troubleshoot-common-errors"></a>Risolvere gli errori comuni

Se l'applicazione non riesce a connettersi al server flessibile del database di Azure per MySQL, in genere indica un problema con uno dei seguenti elementi:

* Connessione crittografata tramite TLS/SSL: il server flessibile supporta solo connessioni crittografate con Transport Layer Security (TLS 1,2) e tutte le **connessioni in ingresso con tls 1,0 e tls 1,1 verranno negate**. Non è possibile disabilitare o modificare la versione di TLS. Altre informazioni sulla [connettività crittografata con Transport Layer Security (TLS 1,2) nel database di Azure per MySQL-server flessibile](./how-to-connect-tls-ssl.md).
- Server flessibile in *accesso privato (integrazione VNet)*: assicurarsi di connettersi dall'interno della stessa rete virtuale del server flessibile. Vedere [rete virtuale nel server flessibile database di Azure per MySQL]<!--(./concepts-networking-virtual-network.md)-->
- Server flessibile con *accesso pubblico (indirizzi IP consentiti)*, assicurarsi che il firewall sia configurato per consentire le connessioni dal client. Vedere [creare e gestire regole flessibili del firewall del server usando il portale di Azure](./how-to-manage-firewall-portal.md).
* Configurazione del firewall client: il firewall del client deve consentire le connessioni al server di database. È necessario consentire gli indirizzi IP e le porte del server, nonché i nomi di applicazioni, ad esempio MySQL, in alcuni firewall.
* Errore dell'utente: è possibile che siano presenti parametri di connessione digitati in modo non conforme, ad esempio il nome del server nella stringa di connessione.

### <a name="resolve-connectivity-issues"></a>Risolvere i problemi di connettività

* Per altre informazioni sulle connessioni crittografate, vedere [la pagina relativa alla connettività crittografata con Transport Layer Security (TLS 1,2) nel database di Azure per MySQL-server flessibile](./how-to-connect-tls-ssl.md) -->.
* Se si usa **l'accesso pubblico (indirizzi IP consentiti)**, configurare [le regole del firewall](./how-to-manage-firewall-portal.md) per consentire l'indirizzo IP del client. Ai soli fini di test temporanei, impostare una regola del firewall usando 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo IP finale. Il server verrà così aperto a tutti gli indirizzi IP. Se questo risolve il problema di connettività, rimuovere la regola e creare una regola del firewall per un indirizzo o un intervallo di indirizzi IP adeguatamente limitato.
* Verificare che la porta 3306 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet.
* Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere le stringhe di connessione predefinite nella pagina stringhe di **connessione** disponibile per il server nella portale di Azure per le lingue comuni.

## <a name="next-steps"></a>Passaggi successivi
- [Usare MySQL Workbench per connettersi ed eseguire query sui dati nel server flessibile database di Azure per MySQL](./connect-workbench.md).
- [Usare php per connettersi ed eseguire query sui dati nel server flessibile database di Azure per MySQL](./connect-php.md).
- [Usare Python per connettersi ed eseguire query sui dati nel server flessibile di database di Azure per MySQL](./connect-python.md).
