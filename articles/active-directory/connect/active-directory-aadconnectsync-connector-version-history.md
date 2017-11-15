---
title: Cronologia del rilascio delle versioni dei connettori | Microsoft Docs
description: Questo argomento include l'elenco di tutte le versioni dei connettori per Forefront Identity Manager (FIM) e Microsoft Identity Manager (MIM)
services: active-directory
documentationcenter: 
author: fimguy
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: fimguy
ms.openlocfilehash: e6df124a38c748294e92183df272dc266a0afc51
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="connector-version-release-history"></a>Cronologia di rilascio delle versioni dei connettori
I connettori per Forefront Identity Manager (FIM) e Microsoft Identity Manager (MIM) vengono aggiornati frequentemente.

> [!NOTE]
> L'argomento riguarda solo FIM e MIM. Questi connettori non sono supportati per l'installazione in Azure AD Connect. I Connettori rilasciati sono preinstallati su AADConnect quando si aggiorna alla Build specificata.


L'argomento include l'elenco di tutte le versioni dei connettori rilasciate.

Collegamenti correlati:

* [Scaricare i connettori più recenti](http://go.microsoft.com/fwlink/?LinkId=717495)
* [connettore LDAP generico](active-directory-aadconnectsync-connector-genericldap.md)
* [connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md)
* [connettore per Servizi Web](http://go.microsoft.com/fwlink/?LinkID=226245)
* [connettore PowerShell](active-directory-aadconnectsync-connector-powershell.md)
* [connettore Lotus Domino](active-directory-aadconnectsync-connector-domino.md)

## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Problemi risolti:

* Lotus Notes:
  * Opzione filtro dei file di certificazione personalizzati
  * Nell'importazione della classe ImportOperations è stata corretta la definizione di quali operazioni possono essere eseguite in modalità "Viste" e quali in modalità "Ricerca".
* Generic LDAP:
  * La directory Open LDAP usa DN come ancoraggio anziché entryUUI. Nuova opzione per il connettore GLDAP che consente di modificare l'ancoraggio
* Generic SQL:
  * È stata corretta l'esportazione nel campo di tipo varbinary(max).
  * Quando si aggiungono dati binari da un'origine dati all'oggetto CSEntry, la funzione DataTypeConversion ha esito negativo in zero byte. È stata corretta la funzione DataTypeConversion della classe CSEntryOperationBase.




### <a name="enhancements"></a>Miglioramenti:

* Generic SQL:
  * La possibilità di configurare la modalità per l'esecuzione di stored procedure con parametri denominati o non denominati è stata aggiunta in una finestra di configurazione dell'agente di gestione di SQL generico nella pagina "Parametri globali". Nella pagina "Parametri globali" è presente la casella di controllo con l'etichetta "Usare parametri denominati per eseguire una stored procedure" che è responsabile della modalità di esecuzione di stored procedure con parametri denominati o non denominati.
    * La possibilità di eseguire stored procedure con parametri denominati attualmente funziona solo per i database IBM DB2 e MSSQL. Nei database Oracle e MySQL questo approccio non funziona: 
      * La sintassi SQL di MySQL non supporta i parametri denominati nelle stored procedure.
      * Il driver ODBC per Oracle non supporta i parametri denominati per i parametri denominati nelle stored procedure.

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Problemi risolti:

* Servizi Web generici:
  * Risoluzione di un problema che previene la creazione di un progetto SOAP quando si sono verificati due o più endpoint.
* Generic SQL:
  * Nell'operazione di importazione GSQL non convertiva correttamente l'ora, quando salvato nello spazio del connettore. Il formato di data e ora predefinito per lo spazio del connettore di GSQL è stato modificato da "aaaa-MM-GG: ssZ" a "aaaa-MM-GG: ssZ".

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Problemi risolti:

* Servizi Web generici:
  * Lo strumento Wsconfig non ha convertito correttamente la matrice Json dalla "richiesta di esempio" per il metodo di servizio REST. Si sono verificati problemi con la serializzazione di questa matrice Json per la richiesta REST.
  * Lo strumento Web Service Connector Configuration non supporta l'utilizzo di simboli di spazio nei nomi attributo JSON 
    * Il criterio di sostituzione può essere aggiunto manualmente al file WSConfigTool.exe.config, ad esempio```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes:
  * Se l'opzione **Consenti rilascio di attestati personalizzati per aziende/unità organizzative** è disattivata, il connettore ha esito negativo durante l'esportazione (aggiornamento); dopo il flusso di esportazione tutti gli attributi vengono esportati in Domino, ma al momento dell'esportazione viene restituita una KeyNotFoundException a Sync. 
    * Ciò accade perché l'operazione di ridenominazione non riesce quando tenta di modificare il parametro DN (attributo UserName) modificando uno dei seguenti attributi:  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - o
      - altcommonname

  * Se l'opzione **Allow custom certifiers for Organization/Organizational Units** (Consenti rilascio di attestati personalizzati per aziende/unità organizzative) è abilitata, ma il file di certificazione richiesto è vuoto, si verifica una KeyNotFoundException.

### <a name="enhancements"></a>Miglioramenti:

* Generic SQL:
  * **Scenario: riprogettato. Implementata la funzionalità:** "*"
  * **Descrizione della soluzione:** modificato l'approccio per la [gestione degli attributi di riferimento multivalore](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Problemi risolti:

* Servizi Web generici:
  * Impossibile importare la configurazione del server in presenza di Web Service Connector
  * Web Service Connector non funziona con più servizi Web

* Generic SQL:
  * Nessun tipo di oggetto elencato per l'attributo a valore singolo a cui si fa riferimento
  * L'importazione delta per la strategia Rilevamento modifiche elimina l'oggetto quando il valore viene rimosso alla tabella multivalore
  * OverflowException nel connettore GSQL con DB2 su AS/400

Lotus:
  * Aggiunta l'opzione per abilitare/disabilitare la ricerca delle unità organizzative prima dell'apertura della pagina GlobalParameters

## <a name="114430"></a>1.1.443.0

Data di rilascio: marzo 2017

### <a name="enhancements"></a>Miglioramenti

* Generic SQL:</br>
  **Sintomi dello scenario:** è un limite noto del connettore SQL quando si consente il riferimento a un solo tipo di oggetto e si richiede un riferimento incrociato con i membri. </br>
  **Descrizione della soluzione:** nella fase di elaborazione per i riferimenti in cui si sceglie l'opzione "*", TUTTE le combinazioni di tipi di oggetto saranno restituite al motore di sincronizzazione.

>[!Important]
- In questo modo vengono creati molti segnaposti
- È necessario assicurarsi che il nome sia univoco tra i tipi di oggetto.


* Generic LDAP:</br>
 **Scenario:** anche quando vengono selezionati solo alcuni contenitori nella partizione specifica, la ricerca viene comunque eseguita nell'intera partizione. La partizione specifica sarà filtrata dal servizio di sincronizzazione ma non dal MA, il che potrebbe causare un calo delle prestazioni. </br>

 **Descrizione della soluzione:** il codice del connettore GLDAP modificato consente di esaminare tutti i contenitori e cercare oggetti in ciascuno di essi, anziché cercare nell'intera partizione.


* Lotus Domino:

  **Scenario:** supporto dell'eliminazione della posta di Domino per la rimozione di un utente durante un'esportazione. </br>
  **Soluzione:** supporto dell'eliminazione della posta configurabile per la rimozione di un utente durante un'esportazione.

### <a name="fixed-issues"></a>Problemi risolti:
* Servizi Web generici:
 * Quando si modifica l'URL del servizio nei progetti predefiniti SAP wsconfig tramite lo strumento di configurazione del servizio Web si verifica l'errore seguente: Impossibile trovare una parte del percorso

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Generic LDAP:
 * Il connettore GLDAP non vede tutti gli attributi in AD LDS
 * La procedura guidata si interrompe quando non viene rilevato alcun attributo UPN dallo schema di directory LDAP
 * Le importazioni differenziali generano errori di individuazione non presenti durante l'importazione completa, quando l'attributo "objectclass" non è selezionato
 * La pagina di configurazione "Configurare partizioni e gerarchie" non mostra gli oggetti il cui tipo è uguale alla partizione per i server Novel nel MA Generic  
LDAP. Essi mostravano solo gli oggetti della partizione di RootDSE.


* Generic SQL:
 * Correzione per il bug dell'attributo multivalore non importato per l'importazione differenziale watermark di Generic SQL
 * Quando si esportano valori di attributi multivalore eliminati/aggiunti, non vengono eliminati/aggiunti nell'origine dati.  


* Lotus Notes:
 * Un campo specifico "Cognome" è visualizzato correttamente nel metaverse, tuttavia durante l'esportazione in Notes il valore dell'attributo è Null o Empty.
 * Correzione per l'errore di file di certificazione duplicato
 * Quando si seleziona l'oggetto senza dati nel connettore Lotus Domino con altri oggetti, viene visualizzato l'errore di individuazione durante un'importazione completa.
 * Quando viene eseguita un'importazione differenziale nel connettore Lotus Domino, alla fine dell'esecuzione il servizio Microsoft.IdentityManagement.MA.LotusDomino.Service.exe talvolta restituisce un errore dell'applicazione.
 * L'appartenenza ai gruppi nel complesso funziona bene e viene mantenuta, tranne quando l'esportazione per tentare di rimuovere un utente dall'appartenenza viene completata correttamente con un aggiornamento ma l'utente non viene effettivamente rimosso dall'appartenenza a Lotus Notes.
 * La possibilità di scegliere la modalità di esportazione "Aggiungi elemento in fondo" è stato aggiunta nell'interfaccia utente grafica di Lotus MA per accodare i nuovi elementi in fondo durante l'esportazione per gli attributi multivalore.
 * Il connettore aggiungerà la logica necessaria per eliminare il file dalla cartella di posta elettronica e l'insieme di credenziali degli ID.
 * Eliminare l'appartenenza non funziona per un membro NAB trasversale.
 * L'eliminazione dei valori da un attributo multivalore dovrebbe riuscire

## <a name="111170"></a>1.1.117.0
Data di rilascio: marzo 2016

**Nuovo connettore**  
Versione iniziale del [connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuove funzionalità:**

* Connettore Generic LDAP:
  * Aggiunta del supporto per l'importazione differenziale con Isode.
* Connettore Web Services:
  * Aggiornamento dell’attività csEntryChangeResult e dell’attività setImportErrorCode per consentire che gli errori a livello di oggetto siano restituiti al motore di sincronizzazione.
  * Aggiornamento dei modelli SAP6 e SAP6User per l’uso della nuova funzionalità di errore a livello di oggetto.
* Connettore Lotus Domino:
  * Per l'esportazione è necessario un file di certificazione per ogni rubrica. È ora possibile usare la stessa password per tutti i file di certificazione semplificando la gestione.

**Problemi risolti:**

* Connettore Generic LDAP:
  * Per IBM Tivoli DS alcuni attributi di riferimento non venivano rilevati correttamente.
  * Per Open LDAP, nelle importazioni differenziali, gli spazi vuoti all’inizio e alla fine delle stringhe venivano troncati.
  * Per Novell e NetIQ un'esportazione che sposta un oggetto fra unità organizzative/contenitori e contemporaneamente rinominano l'oggetto non riescono.
* Connettore Web Services:
  * Se il servizio Web aveva più endpoint per lo stesso binding, il connettore non individuava correttamente questi endpoint.
* Connettore Lotus Domino:
  * L’esportazione dell’attributo fullName in un database di posta elettronica in ingresso non funzionava.
  * Le esportazioni che aggiungevano e rimuovevano membri da un gruppo esportavano solo i membri aggiunti.
  * Se un documento di Notes non è valido (l'attributo isValid è impostato su false), l'operazione del connettore non riesce.

## <a name="older-releases"></a>Versioni precedenti
Prima di marzo 2016, i connettori venivano rilasciati come argomenti relativi al supporto.

**Generic LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, settembre 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, marzo 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, gennaio 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, settembre 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, marzo 2014

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, settembre 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, settembre 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, settembre 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, marzo 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, agosto 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, febbraio 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, ottobre 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, agosto 2013

## <a name="troubleshooting"></a>Risoluzione dei problemi 

> [!NOTE]
> Quando si aggiorna Microsoft Identity Manager o AADConnect con l'uso di uno qualsiasi dei connettori ECMA2. 

È necessario aggiornare la definizione del connettore dopo l'aggiornamento per creare la corrispondenza. In caso contrario, nel registro eventi dell'applicazione verrà visualizzato l'avviso ID 6947: "Assembly version in AAD Connector configuration ("X.X.XXX.X") is earlier than the actual version ("X.X.XXX.X") of "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll" (La versione dell'assieme nella configurazione di AAD Connector ("X.X.XXX.X") è precedente alla versione attuale ("X.X.XXX.X") di "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll).

Per aggiornare la definizione:
* Aprire le proprietà per l'istanza del connettore.
* Fare clic sulla scheda Connection/Connect (Connessione/Connect).
  * Immettere la password per l'account del connettore.
* Fare clic su ognuna delle schede delle proprietà a turno.
  * Se questo tipo di connettore dispone di una scheda Partitions (Partizioni) con un pulsante di aggiornamento, fare clic sul pulsante Refresh (Aggiorna) mentre è visualizzata la scheda.
* Dopo avere visualizzato tutte le schede di proprietà, fare clic sul pulsante OK per salvare le modifiche.


## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
