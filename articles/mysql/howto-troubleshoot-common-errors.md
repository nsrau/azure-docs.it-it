---
title: Risolvere gli errori comuni - Database di Azure per MySQL
description: Informazioni su come risolvere gli errori di migrazione comuni rilevati dai nuovi utenti del servizio Database di Azure per MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: f64d4d2b9acbe0e6585ca546c915b82d2d1dbbc4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737193"
---
# <a name="common-errors"></a>Errori comuni

Database di Azure per MySQL è un servizio di database completamente gestito basato sulla versione Community di MySQL. L'esperienza MySQL in un ambiente di servizio gestito può risultare diversa rispetto all'esecuzione di MySQL in un ambiente personale. In questo articolo verranno illustrati alcuni degli errori comuni che gli utenti possono rilevare quando eseguono per la prima volta la migrazione o lo sviluppo nel servizio Database di Azure per MySQL.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Errori dovuti a mancanza del privilegio SUPER e del ruolo DBA

Il privilegio SUPER e il ruolo DBA non sono supportati nel servizio. È quindi possibile che si verifichino alcuni degli errori comuni elencati di seguito:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERRORE 1419: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable) (Non è disponibile il privilegio SUPER e la registrazione binaria è abilitata. È consigliabile usare la variabile log_bin_trust_function_creators meno sicura)

È possibile che l'errore indicato si verifichi durante la creazione di una funzione, di un trigger come indicato di seguito o dell'importazione di uno schema. Le dichiarazioni DDL come CREATE FUNCTION o CREATE TRIGGER vengono scritte nel log binario, per consentire alla replica secondaria di eseguirle. Il thread SQL della replica ha privilegi completi, che possono essere sfruttati per elevare i privilegi. Per evitare questo rischio per i server in cui è abilitata la registrazione binaria, il motore di MySQL richiede agli autori di funzioni archiviate di avere il privilegio SUPER, oltre al privilegio CREATE ROUTINE consueto obbligatorio. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Soluzione** :  Per risolvere l'errore, impostare log_bin_trust_function_creators su 1 dal pannello [Parametri del server](howto-server-parameters.md) nel portale, eseguire le istruzioni DDL o importare lo schema per creare gli oggetti specifici e ripristinare il valore precedente del parametro log_bin_trust_function_creators dopo la creazione.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERRORE 1227 (42000) alla riga 101: Access denied; you need (at least one of) the SUPER privilege(s) for this operation. Operation failed with exitcode 1 (Accesso negato. L'operazione richiede almeno uno dei privilegi SUPER. L'operazione non è riuscita con codice 1)

È possibile che l'errore precedente si verifichi durante l'importazione di un file di dump o durante la creazione di una procedura che contiene [definer](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Soluzione** :  per risolvere questo errore, l'utente amministratore può concedere privilegi per la creazione o l'esecuzione di procedure mediante l'esecuzione del comando GRANT, come indicato negli esempi seguenti:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
In alternativa è possibile sostituire i definer con il nome dell'utente amministratore che esegue il processo di importazione, come illustrato di seguito.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>Passaggi successivi
Se la risposta cercata non è disponibile, prendere in considerazione quanto segue:
- Pubblicare la domanda nella [pagina di domande di Microsoft Q&A](/answers/topics/azure-database-mysql.html) o in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Inviare un messaggio di posta elettronica al team di Database di Azure per MySQL [@Ask Database di Azure per MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Questo indirizzo di posta elettronica non è un alias del supporto tecnico.
- Contattare il supporto di Azure, [creando un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.
- Per fornire commenti e suggerimenti o richiedere nuove funzionalità, creare una richiesta tramite [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
