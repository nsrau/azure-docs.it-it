---
title: Connettività SSL/TLS-database di Azure per MySQL
description: Informazioni per la configurazione del Database di Azure per MySQL e delle applicazioni associate per usare correttamente le connessioni SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 2421f8a9396b47d04db35a7cad843f6baa6f6177
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416104"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Connettività SSL/TLS nel database di Azure per MySQL

Il Database di Azure per MySQL supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="ssl-default-settings"></a>Impostazioni predefinite SSL

Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MySQL.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile.

Per impostazione predefinita, l'opzione di applicazione delle connessioni SSL è attiva quando si esegue il provisioning di un nuovo Database di Azure per il server MySQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni**, selezionare **Stringhe di connessione**. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

Per informazioni su come abilitare o disabilitare la connessione SSL durante lo sviluppo dell'applicazione, consultare [Come configurare SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Imposizione di TLS nel database di Azure per MySQL

Database di Azure per MySQL supporta la crittografia per i client che si connettono al server di database usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete sicure tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

### <a name="tls-settings"></a>Impostazioni di TLS

Database di Azure per MySQL offre la possibilità di applicare la versione TLS per le connessioni client. Per applicare la versione TLS, usare l'impostazione dell'opzione **versione minima di TLS** . Per questa impostazione di opzione sono consentiti i valori seguenti:

|  Impostazione minima di TLS             | Versione TLS del client supportata                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (impostazione predefinita) | Non sono necessari TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e versioni successive           |
| TLS1_1                           | TLS 1,1, TLS 1,2 e versioni successive                   |
| TLS1_2                           | TLS versione 1,2 e successive                     |


Se ad esempio si imposta il valore della versione minima dell'impostazione TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, l'impostazione di questa opzione su 1,2 significa che è possibile consentire solo le connessioni dai client che usano TLS 1.2 + e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

> [!Note] 
> Il valore predefinito di TLS per database di Azure per MySQL è disabilitato per tutti i nuovi server.
>
> Attualmente le versioni di TLS supportate da database di Azure per MySQL sono TLS 1,0, 1,1 e 1,2. Una volta applicata una versione minima specifica di TLS, non è possibile modificarla in disabilitata.

Per informazioni su come impostare l'impostazione TLS per il database di Azure per MySQL, vedere [How to configure TLS setting](howto-tls-configurations.md).

## <a name="next-steps"></a>Passaggi successivi

- [Librerie di connessione per database di Azure per MySQL](concepts-connection-libraries.md)
- Informazioni su come [configurare SSL](howto-configure-ssl.md)
- Informazioni su come [configurare TLS](howto-tls-configurations.md)
