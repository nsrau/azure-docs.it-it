---
title: 'Convalida dell''input: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: Procedure di mitigazione delle minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: c0d90f7c6ad136cd1a558f6158cf734de51b9538
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="security-frame-input-validation--mitigations"></a>Infrastruttura di sicurezza: convalida dell'input - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Applicazione Web** | <ul><li>[Disabilitare gli script XSLT per tutte le trasformazioni con fogli di stile non attendibili](#disable-xslt)</li><li>[Verificare che ogni pagina che potrebbe includere contenuti controllabili dall'utente rifiuti esplicitamente l'analisi MIME automatica](#out-sniffing)</li><li>[Applicare la protezione avanzata o la disabilitazione della risoluzione di entità XML](#xml-resolution)</li><li>[Verifica della canonizzazione degli URL nelle applicazioni che utilizzano http.sys](#app-verification)</li><li>[Verificare la presenza dei controlli appropriati quando si accettano file dagli utenti](#controls-users)</li><li>[Verificare che nell'applicazione Web vengano usati parametri indipendenti dai tipi per l'accesso ai dati](#typesafe)</li><li>[Usare classi di associazione di modelli separate o elenchi di filtri di associazione per prevenire la vulnerabilità dell'assegnazione di massa in MVC](#binding-mvc)</li><li>[Codificare l'output Web non attendibile prima del rendering](#rendering)</li><li>[Eseguire la convalida dell'input e applicare filtri a tutte le proprietà del modello di tipo stringa](#typemodel)</li><li>[Applicazione della purificazione nei campi modulo che accettano tutti i caratteri, ad esempio un editor di testo RTF](#richtext)</li><li>[Non assegnare elementi DOM a sink senza codifica incorporata](#inbuilt-encode)</li><li>[Convalidare come chiusi o sicuri tutti i reindirizzamenti nell'applicazione](#redirect-safe)</li><li>[Implementare la convalida dell'input in un tutti i parametri di tipo stringa accettati dai metodi del controller](#string-method)</li><li>[Impostare il limite massimo di timeout per l'elaborazione di espressioni regolari per impedire attacchi DoS causati da espressioni regolari errate](#dos-expression)</li><li>[Evitare di usare Html.Raw nelle visualizzazioni Razor](#html-razor)</li></ul> | 
| **Database** | <ul><li>[Non usare query dinamiche nelle stored procedure](#stored-proc)</li></ul> |
| **API Web** | <ul><li>[Verificare l'esecuzione della convalida dei modelli nei metodi di API Web](#validation-api)</li><li>[Implementare la convalida dell'input in tutti i parametri di tipo stringa accettati dai metodi di API Web](#string-api)</li><li>[Verificare che nell'API Web vengano usati parametri indipendenti dai tipi per l'accesso ai dati](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Usare query SQL con parametri per Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF: convalida dell'input tramite l'associazione allo schema](#schema-binding)</li><li>[WCF: convalida dell'input tramite controlli parametro](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Disabilitare gli script XSLT per tutte le trasformazioni con fogli di stile non attendibili

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Sicurezza XSLT ](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [Proprietà XsltSettings.EnableScript](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Passaggi** | XSLT supporta l'inserimento di script all'interno dei fogli di stile con l'elemento `<msxml:script>`. Ciò consente di usare funzioni personalizzate in una trasformazione XSLT. Lo script viene eseguito nel contesto del processo che esegue la trasformazione. Lo script XSLT può essere disabilitato in un ambiente non attendibile per impedire l'esecuzione di codice non attendibile. *Se si usa .NET*, gli script XSLT sono disabilitati per impostazione predefinita. È tuttavia necessario verificare che non siano stati abilitati in modo esplicito tramite la proprietà `XsltSettings.EnableScript`.|

### <a name="example"></a>Esempio 

```C#
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Esempio
Se si usa MSXML 6.0, gli script XSLT sono disabilitati per impostazione predefinita. È tuttavia necessario verificare che non siano stati abilitati in modo esplicito tramite la proprietà AllowXsltScript dell'oggetto DOM XML. 

```C#
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Esempio
Se si usa MSXML 5 o versione inferiore, gli script XSLT sono abilitati per impostazione predefinita e devono essere disabilitati in modo esplicito. Impostare la proprietà AllowXsltScript dell'oggetto DOM XML su false. 

```C#
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Verificare che ogni pagina che potrebbe includere contenuti controllabili dall'utente rifiuti esplicitamente l'analisi MIME automatica

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [IE8 Security Part V: Comprehensive Protection](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx) (Sicurezza di IE8 parte V: protezione completa)  |
| **Passaggi** | <p>Per ogni pagina che potrebbe includere contenuti controllabili dall'utente, è necessario usare l'intestazione HTTP `X-Content-Type-Options:nosniff`. Per rispettare questo requisito, è possibile impostare l'intestazione necessaria pagina per pagina solo per le pagine che potrebbero includere contenuti controllabili dall'utente oppure eseguire l'impostazione a livello globale per tutte le pagine nell'applicazione.</p><p>A ogni tipo di file fornito da un server Web è associato un [tipo MIME](http://en.wikipedia.org/wiki/Mime_type) (denominato anche *content-type*) che descrive la natura del contenuto (immagine, testo, applicazione e così via).</p><p>L'intestazione X-Content-Type-Options è un'intestazione HTTP che consente agli sviluppatori di specificare che il contenuto non deve essere sottoposto ad analisi MIME. Questa intestazione è progettata per mitigare gli attacchi basati sull'analisi MIME. Il supporto per questa intestazione è stato aggiunto in Internet Explorer 8 (IE8).</p><p>Solo gli utenti di Internet Explorer 8 (IE8) potranno usufruire di X-Content-Type-Options. Le versioni precedenti di Internet Explorer attualmente non rispettano l'intestazione X-Content-Type-Options.</p><p>Internet Explorer 8 e le versioni successive sono gli unici browser principali che implementano una funzionalità di rifiuto esplicito dell'analisi MIME. Se e quando gli altri browser principali (Firefox, Safari e Chrome) implementeranno funzionalità simili, questa raccomandazione verrà aggiornata per includere anche la sintassi per tali browser.</p>|

### <a name="example"></a>Esempio
Per abilitare l'intestazione necessaria a livello globale per tutte le pagine nell'applicazione, eseguire una di queste operazioni: 

* Aggiungere l'intestazione nel file web.config se l'applicazione è ospitata da Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Aggiungere l'intestazione tramite Application\_BeginRequest globale 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementare un modulo HTTP personalizzato 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* È possibile abilitare l'intestazione necessaria solo per pagine specifiche aggiungendola a singole risposte: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Applicare la protezione avanzata o la disabilitazione della risoluzione di entità XML

| Title                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Espansione di entità XML](http://capec.mitre.org/data/definitions/197.html), [Attacchi Denial of Service e difese in XML](http://msdn.microsoft.com/magazine/ee335713.aspx), [Cenni preliminari sulla sicurezza MSXML](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [Procedure consigliate per la protezione del codice MSXML](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [Informazioni di riferimento sul protocollo NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [Risoluzione di riferimenti esterni](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Passaggi**| <p>Nonostante non sia largamente usata, esiste una funzionalità di XML che consente al parser XML di espandere macro entità con valori definiti all'interno del documento stesso o da origini esterne. Ad esempio, il documento potrebbe definire un'entità "companyname" con il valore "Microsoft" in modo che ogni occorrenza del testo "&companyname;" nel documento venga automaticamente sostituita con il testo "Microsoft". In alternativa, il documento potrebbe definire un'entità "MSFTStock" che fa riferimento a un servizio Web esterno per recuperare il valore corrente delle azioni Microsoft.</p><p>Ogni occorrenza di "&MSFTStock;" nel documento verrà quindi automaticamente sostituita con il prezzo corrente del titolo azionario. Questa funzionalità può tuttavia essere usata in modo improprio per creare condizioni di Denial of Service (DoS). Un utente malintenzionato è possibile nidificare più entità per creare un'espansione esponenziale XML di validità che utilizza tutta la memoria disponibile nel sistema. </p><p>In alternativa, può creare un riferimento esterno che trasmette una quantità infinita di dati o semplicemente blocca il thread. Di conseguenza, tutti i team devono disabilitare interamente la risoluzione di entità XML interne e/o esterne se non viene usata dall'applicazione oppure, se tale funzionalità è assolutamente necessaria, limitare manualmente la quantità di memoria e di tempo utilizzabile dall'applicazione per la risoluzione di entità. Se la risoluzione di entità non è richiesta dall'applicazione, disabilitarla. </p>|

### <a name="example"></a>Esempio
Per il codice .NET Framework, è possibile usare gli approcci seguenti:

```C#
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Si noti che il valore predefinito di `ProhibitDtd` in `XmlReaderSettings` è true, ma in `XmlTextReader` è false. Se si usa XmlReaderSettings, non è necessario impostare ProhibitDtd su true in modo esplicito, ma è consigliabile ai fini della sicurezza. Si noti anche che la classe XmlDocument consente la risoluzione di entità per impostazione predefinita. 

### <a name="example"></a>Esempio
Per disabilitare la risoluzione di entità per le classi XmlDocument, usare l'overload `XmlDocument.Load(XmlReader)` del metodo Load e impostare le proprietà appropriate nell'argomento XmlReader per disabilitare la risoluzione, come illustrato nel codice seguente: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Esempio
Se non è possibile disabilitare la risoluzione di entità per l'applicazione, impostare la proprietà XmlReaderSettings.MaxCharactersFromEntities su un valore ragionevole in base alle esigenze dell'applicazione. Verrà così limitato l'impatto dei potenziali attacchi DoS con espansione esponenziale. Il codice seguente offre un esempio di questo approccio: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Esempio
Se è necessario risolvere entità incorporate ma non entità esterne, impostare la proprietà XmlReaderSettings.XmlResolver su null. Ad esempio:  

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Si noti che in MSXML6, la proprietà ProhibitDTD è impostata su true (in modo da disabilitare l'elaborazione DTD) per impostazione predefinita. Per il codice Apple OSX/iOS, è possibile usare due parser XML: NSXMLParser e libXML2. 

## <a id="app-verification"></a>Verifica della canonizzazione degli URL nelle applicazioni che utilizzano http.sys

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Tutte le applicazioni che usano http.sys devono seguire le linee guida seguenti:</p><ul><li>Limitare la lunghezza degli URL a non oltre 16.384 caratteri (ASCII o Unicode). Questa è la lunghezza massima assoluta degli URL in base all'impostazione predefinita di Internet Information Services (IIS) 6. Se possibile, i siti Web devono cercare di mantenere una lunghezza inferiore a tale massimo.</li><li>Usare le classi di I/O file standard di .NET Framework (come FileStream), perché sfruttano le regole di canonizzazione di .NET FX.</li><li>Compilare in modo esplicito un elenco di nomi file noti consentiti.</li><li>Rifiutare in modo esplicito i nomi file noti che non verranno gestiti con rifiuti tramite UrlScan, ossia i file con estensione exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, printer, ini, pol e dat.</li><li>Rilevare le eccezioni seguenti:<ul><li>System.ArgumentException (per i nomi di dispositivo)</li><li>System.NotSupportedException (per i flussi di dati)</li><li>System.IO.FileNotFoundException (per i nomi file preceduti da carattere di escape non validi)</li><li>System.IO.DirectoryNotFoundException (per le directory precedute da carattere di escape non valide)</li></ul></li><li>*Non* chiamare API di I/O file Win32. In caso di URL non valido, restituire automaticamente un errore 400 all'utente e registrare l'errore effettivo.</li></ul>|

## <a id="controls-users"></a>Verificare la presenza dei controlli appropriati quando si accettano file dagli utenti

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Caricamento di file senza restrizioni), [File Signature Table](http://www.garykessler.net/library/file_sigs.html) (Tabella delle firme dei file) |
| **Passaggi** | <p>I file caricati rappresentano un rischio significativo per le applicazioni.</p><p>Il primo passaggio di numerosi attacchi consiste nell'inserire codice nel sistema da attaccare. È quindi sufficiente trovare un modo per far sì che il codice venga eseguito. Il caricamento di un file consente all'utente malintenzionato di portare a termine il primo passaggio. Le conseguenze del caricamento di file senza restrizioni possono variare dalla completa acquisizione della proprietà del sistema al sovraccarico del file system o del database, all'inoltro di attacchi ai sistemi back-end e al semplice danneggiamento.</p><p>Ciò dipende da come il file caricato viene usato dall'applicazione e soprattutto dalla posizione in cui viene archiviato. La convalida sul lato server dei caricamenti di file non è disponibile. Per la funzionalità di caricamento file devono essere implementati i controlli di sicurezza seguenti:</p><ul><li>Controllo dell'estensione di file (deve essere accettato solo un set valido di tipi di file consentiti)</li><li>Limite di dimensione massima dei file</li><li>I file non devono essere caricati in webroot, ma in una directory in un'unità non di sistema</li><li>Deve essere seguita la convenzione di denominazione in modo da garantire una certa casualità nel nome del file caricato e impedire le sovrascritture di file</li><li>I file devono essere sottoposti ad analisi antivirus prima della scrittura sul disco</li><li>Verificare che il nome file e tutti gli altri metadati (ad esempio, il percorso file) vengano convalidati in relazione alla presenza di caratteri dannosi</li><li>La firma del formato di file deve essere controllata per impedire a un utente di caricare un file mascherato (ad esempio, caricare un file EXE modificandone l'estensione in txt)</li></ul>| 

### <a name="example"></a>Esempio
Per informazioni dettagliate sull'ultimo punto relativo alla convalida della firma del formato di file, vedere la classe seguente: 

```C#
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Verificare che nell'applicazione Web vengano usati parametri indipendenti dai tipi per l'accesso ai dati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Se si usa la raccolta Parameters, SQL considera l'input come un valore letterale anziché come codice eseguibile. La raccolta Parameters può essere usata per imporre vincoli di tipo e lunghezza sui dati di input. I valori non compresi nell'intervallo attivano un'eccezione. Se non vengono usati parametri SQL indipendenti dai tipi, gli utenti malintenzionati potrebbero eseguire attacchi di tipo injection incorporati nell'input non filtrato.</p><p>Quando si costruiscono query SQL, usare parametri indipendenti dai tipi per evitare gli attacchi SQL injection che possono verificarsi con input non filtrato. È possibile usare parametri indipendenti dai tipi con stored procedure e con istruzioni SQL dinamiche. I parametri vengono considerati dal database come valori letterali e non come codice eseguibile. Viene anche eseguito il controllo del tipo e della lunghezza dei parametri.</p>|

### <a name="example"></a>Esempio 
Il codice seguente illustra come usare parametri indipendenti dai tipi con SqlParameterCollection quando si chiama una stored procedure. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Nell'esempio di codice precedente, la lunghezza del valore di input non può essere maggiore di 11 caratteri. Se i dati non sono conformi alla lunghezza o al tipo definito dal parametro, la classe SqlParameter genera un'eccezione. 

## <a id="binding-mvc"></a>Usare classi di associazione di modelli separate o elenchi di filtri di associazione per prevenire la vulnerabilità dell'assegnazione di massa in MVC

| Title                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Attributi dei metadati](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [Public Key Security Vulnerability And Mitigation](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation) (Vulnerabilità della sicurezza delle chiavi pubbliche e mitigazione), [Guida completa all'assegnazione di massa in ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Introduzione a Entity Framework con MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Passaggi** | <ul><li>**Quando è consigliabile controllare eventuali vulnerabilità all'overposting?** Le vulnerabilità all'overposting si possono verificare ovunque si associno classi modello dall'input utente. I framework come MVC possono rappresentare i dati utente in classi .NET personalizzate, tra cui Plain Old CLR Object (POCO). MVC popola automaticamente queste classi modello con i dati della richiesta, offrendo una pratica rappresentazione per la gestione dell'input utente. Quando queste classi includono proprietà che non devono essere impostate dall'utente, l'applicazione può essere vulnerabile ad attacchi di overposting, che consentono all'utente un controllo sui dati non previsto dall'applicazione. Così come l'associazione di modelli MVC, le tecnologie di accesso a database come i mapper relazionali/a oggetti come Entity Framework spesso supportano anche l'uso di oggetti POCO per rappresentare i dati dei database. Queste classi del modello di dati consentono di gestire i dati dei database con la stessa praticità offerta da MVC per la gestione dell'input utente. Dato che MVC e il database supportano modelli simili, come gli oggetti POCO, le stesse classi sembrano facilmente riusabili per entrambi gli scopi. Questa procedura non riesce a mantenere la separazione dei compiti e offre un'area comune in cui proprietà non previste vengono esposte all'associazione di modelli, consentendo attacchi di overposting.</li><li>**Perché non è consigliabile usare classi modello del database non filtrate come parametri per le azioni MVC?** Perché l'associazione di modelli MVC associa tutti gli elementi nella classe. Un utente malintenzionato può inviare una richiesta HTTP includendo anche dati non mostrati nella visualizzazione e questi verranno associati da MVC perché l'azione indica che la classe del database è la forma di dati da accettare come input utente.</li><li>**Perché è necessario prestare attenzione alla forma usata per l'associazione di modelli?** L'uso dell'associazione di modelli ASP.NET MVC con modelli eccessivamente estesi espone un'applicazione ad attacchi di overposting. L'overposting potrebbe consentire a utenti malintenzionati di modificare dati dell'applicazione non previsti dallo sviluppatore, ad esempio sostituendo il prezzo di un articolo o i privilegi di sicurezza di un account. Le applicazioni devono usare modelli di associazione specifici dell'azione (o specifici elenchi di filtri delle proprietà consentite) per fornire un contratto esplicito per l'input non attendibile da consentire tramite l'associazione di modelli.</li><li>**La presenza di modelli di associazione separati è solo una duplicazione del codice?** No, è una separazione dei compiti. Se si riusano i modelli del database nei metodi delle azioni, qualsiasi proprietà o proprietà secondaria nella classe potrà essere impostata dall'utente in una richiesta HTTP. Se ciò non corrisponde al comportamento desiderato per MVC, è necessario usare un elenco di filtri o una forma di classe separata per indicare a MVC quali dati possono invece provenire dall'input utente.</li><li>**Se si usano modelli di associazione separati per l'input utente, è necessario duplicare tutti gli attributi di annotazione dei dati?** Non necessariamente. È possibile usare MetadataTypeAttribute nella classe modello del database per il collegamento ai metadati in una classe di associazione di modelli. Si noti soltanto che il tipo a cui fa riferimento MetadataTypeAttribute deve essere un subset del tipo di riferimento (può avere meno proprietà, ma non di più).</li><li>**Lo spostamento di dati tra i modelli dell'input utente e i modelli del database è un'attività tediosa. È possibile copiare semplicemente tutte le proprietà con la reflection?** Sì. Le uniche proprietà incluse nei modelli di associazione sono quelle che sono state accertate come sicure per l'input utente. Non esistono motivi di sicurezza che impediscono di usare la reflection per copiare tutte le proprietà comuni tra i due modelli.</li><li>**Come funziona [Bind(Exclude ="â€¦")]? È possibile usare questo attributo anziché modelli di associazione separati?** Questo approccio non è consigliabile. Usando [Bind(Exclude ="â€¦")], qualsiasi nuova proprietà è associabile per impostazione predefinita. Quando viene aggiunta una nuova proprietà, è necessario ricordare un passaggio aggiuntivo per garantire la sicurezza e non si ottiene una progettazione sicura per impostazione predefinita. Dipendere dal fatto che lo sviluppatore controlli questo elenco ogni volta che viene aggiunta una proprietà è rischioso.</li><li>**[Bind(Include ="â€¦")] è utile per le operazioni di modifica?** No. [Bind(Include ="â€¦")] è adatto solo per operazioni di tipo INSERT, ossia per l'aggiunta di nuovi dati. Per operazioni di tipo UPDATE, ossia per la revisione dei dati esistenti, usare un altro approccio, come l'uso di modelli di associazione separati o il passaggio di un elenco esplicito delle proprietà consentite a UpdateModel o TryUpdateModel. Con l'aggiunta di un attributo [Bind(Include ="â€¦")] in un'operazione di modifica, MVC creerà un'istanza dell'oggetto e imposterà solo le proprietà elencate, mantenendo i valori predefiniti per tutte le altre. Quando i dati vengono resi persistenti, sostituirà interamente l'entità esistente, reimpostando i valori predefiniti per tutte le proprietà omesse. Ad esempio, se la proprietà IsAdmin è stata omessa in un attributo [Bind(Include ="â€¦")] per un'operazione di modifica, qualsiasi utente il cui nome è stato modificato tramite tale azione verrà reimpostato su IsAdmin = false. Qualsiasi utente modificato perderà così lo stato di amministratore. Se si vuole impedire l'aggiornamento di determinate proprietà, usare uno degli altri approcci descritti sopra. Si noti che alcune versioni degli strumenti MVC generano classi controller con [Bind(Include ="â€¦")] nelle azioni di modifica e prevedono che la rimozione di una proprietà dall'elenco impedisca attacchi di overposting. Come descritto sopra, tuttavia, questo approccio non funziona come previsto e reimposta invece i dati nelle proprietà omesse sui valori predefiniti.</li><li>**Per le operazioni di creazione, esistono controindicazioni all'uso di [Bind(Include ="â€¦")] anziché di modelli di associazione separati?** Sì. Per prima cosa, questo approccio non funziona per gli scenari di modifica e richiede quindi la gestione di due approcci separati per la mitigazione di tutte le vulnerabilità all'overposting. In secondo luogo, i modelli di associazione separati impongono la separazione dei compiti tra la forma usata per l'input utente e la forma usata per la persistenza, una funzionalità non offerta da [Bind(Include ="â€¦")]. In terzo luogo, [Bind(Include ="â€¦")] può gestire solo le proprietà di livello superiore e non è quindi possibile consentire solo parti di proprietà secondarie (come "Details.Name") nell'attributo. Infine e forse soprattutto, l'uso di [Bind(Include ="â€¦")] comporta un passaggio aggiuntivo da ricordare ogni volta che la classe viene usata per l'associazione di modelli. Se un nuovo metodo di azione esegue l'associazione diretta alla classe dati senza includere un attributo [Bind(Include ="â€¦")], può essere vulnerabile ad attacchi di overposting. Di conseguenza, l'approccio [Bind(Include ="â€¦")] è un po' meno sicuro per impostazione predefinita. Se si usa [Bind(Include ="â€¦")], ricordare di specificarlo ogni volta che le classi dati vengono incluse come parametri di metodi di azione.</li><li>**Per le operazioni di creazione, l'inserimento dell'attributo [Bind(Include ="â€¦")] nella classe modello evita di dover ricordare di inserire l'attributo in ogni metodo di azione?** Questo approccio funziona in alcuni casi. L'uso di [Bind(Include ="â€¦")] nel tipo di modello (anziché nei parametri di azione che usano la classe) evita di dover ricordare di includere l'attributo [Bind(Include ="â€¦")] in ogni metodo di azione. Usando l'attributo direttamente nella classe, si crea di fatto una superficie di attacco separata di tale classe ai fini dell'associazione di modelli. Questo approccio, tuttavia, supporta una sola forma di associazione di modelli per classe modello. Se un metodo di azione deve consentire l'associazione di modelli di un campo (come ad esempio un'azione riservata all'amministratore che aggiorna i ruoli utente) mentre le altre azioni devono impedire l'associazione di modelli di tale campo, questo approccio non funziona. Ogni classe può avere una sola forma di associazione di modelli. Se azioni diverse richiedono forme di associazione di modelli diverse, devono rappresentare queste forme separate usando classi di associazione di modelli separate oppure attributi [Bind(Include ="â€¦")] separati nei metodi di azione.</li><li>**Che cosa sono i modelli di associazione? Sono uguali ai modelli di visualizzazione?** Si tratta di due concetti correlati. Il termine modello di associazione indica una classe modello usata in un'azione come elenco di parametri, ossia la forma passata dall'associazione di modelli MVC al metodo di azione. Il termine modello di visualizzazione indica una classe modello passata da un metodo di azione a una visualizzazione. L'uso di un modello specifico per la visualizzazione è un approccio comune per passare dati da un metodo di azione a una visualizzazione. Spesso, questa forma è particolarmente adatta per l'associazione del modello e il modello di visualizzazione termine consente di fare riferimento lo stesso modello utilizzato in entrambe le posizioni. Per la precisione, questa procedura riguarda specificamente i modelli di associazione e si concentra sulla forma passata all'azione, rilevante ai fini dell'assegnazione di massa.</li></ul>| 

## <a id="rendering"></a>Codificare l'output Web non attendibile prima del rendering

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, Web Form, MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [How to prevent Cross-site scripting in ASP.NET](http://msdn.microsoft.com/library/ms998274.aspx) (Come impedire attacchi tramite script da altri siti in ASP.NET), [Cross-site Scripting](http://cwe.mitre.org/data/definitions/79.html) (Attacchi tramite script da altri siti), [XSS (Cross Site Scripting) Prevention Cheat Sheet](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) (Foglio informativo sulla prevenzione degli attacchi tramite script da altri siti) |
| **Passaggi** | Gli attacchi tramite script da altri siti (comunemente abbreviati in XSS, Cross-Site Scripting) sono un vettore di attacco per i servizi online o qualsiasi applicazione/componente che utilizza input dal Web. Le vulnerabilità XSS possono consentire a un utente malintenzionato di eseguire script sul computer di un altro utente tramite un'applicazione Web vulnerabile. Gli script dannosi possono essere usati per rubare cookie o manomettere in altro modo il computer di una vittima tramite JavaScript. È possibile impedire attacchi XSS convalidando l'input utente e verificando che il formato e la codifica siano corretti prima di eseguirne il rendering in una pagina Web. La convalida dell'input e la codifica dell'output possono essere eseguite con Web Protection Library. Per il codice gestito (C\#, VB.net e così via), usare uno o più metodi di codifica appropriati di Web Protection (Anti-XSS) Library, a seconda del contesto in cui si manifesta l'input utente:| 

### <a name="example"></a>Esempio

```C#
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Eseguire la convalida dell'input e applicare filtri a tutte le proprietà del modello di tipo stringa

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Adding Validation](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation) (Aggiunta della convalida), [Validating Model Data in an MVC Application](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) (Convalida dei dati del modello in un'applicazione MVC), [Principi guida per le applicazioni ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passaggi** | <p>Tutti i parametri di input devono essere convalidati prima di essere usati nell'applicazione per garantire la protezione dell'applicazione da input utente dannosi. Convalidare i valori di input usando espressioni regolari sul lato server con una strategia di convalida tramite elenchi degli elementi consentiti. I parametri o gli input utente non purificati passati ai metodi possono causare vulnerabilità a code injection.</p><p>Per le applicazioni Web, i punti di ingresso possono includere anche campi modulo, stringhe di query, cookie, intestazioni HTTP e parametri del servizio Web.</p><p>All'associazione di modelli devono essere eseguiti i controlli di convalida dell'input seguenti:</p><ul><li>Nelle proprietà dei modelli deve essere usata l'annotazione RegularExpression, per l'accettazione dei caratteri e della lunghezza massima consentiti</li><li>I metodi del controller devono eseguire la convalida ModelState</li></ul>|

## <a id="richtext"></a>Applicazione della purificazione nei campi modulo che accettano tutti i caratteri, ad esempio un editor di testo RTF

| Title                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Encode Unsafe Input](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3) (Codificare l'input non sicuro), [HtmlSanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Passaggi** | <p>Identificare tutti i tag di markup statici che si vogliono usare. Una procedura comune consiste nel limitare la formattazione a elementi HTML sicuri, come `<b>` (grassetto) e `<i>` (corsivo).</p><p>Prima della scrittura dei dati, applicare la codifica HTML. In questo modo, eventuali script dannosi diventeranno sicuri perché verranno gestiti come testo anziché come codice eseguibile.</p><ol><li>Disabilitare la convalida delle richieste ASP.NET aggiungendo l'attributo ValidateRequest="false" alla direttiva @ Page</li><li>Codificare l'input di stringa con il metodo HtmlEncode</li><li>Usare StringBuilder e chiamare il relativo metodo Replace per rimuovere in modo selettivo la codifica negli elementi HTML che si vuole consentire</li></ol><p>La direttiva di pagina nei riferimenti disabilita la convalida delle richieste ASP.NET con l'impostazione `ValidateRequest="false"`, applica la codifica HTML all'input e consente in modo selettivo `<b>` e `<i>`. In alternativa, è anche possibile usare una libreria .NET per la purificazione HTML.</p><p>HtmlSanitizer è una libreria .NET per la pulizia di documenti e frammenti HTML dei costrutti che possono causare attacchi XSS. Per l'analisi, la manipolazione e il rendering di HTML e CSS usa AngleSharp. È possibile installare HtmlSanitizer come pacchetto NuGet e passare l'input utente attraverso i metodi di purificazione HTML o CSS pertinenti, a seconda dei casi, sul lato server. Si noti che la purificazione deve essere presa in considerazione come controllo di sicurezza solo come ultima opzione.</p><p>La convalida dell'input e la codifica dell'output sono considerati controlli di sicurezza più efficienti.</p> |

## <a id="inbuilt-encode"></a>Non assegnare elementi DOM a sink senza codifica incorporata

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Molte funzioni JavaScript non eseguono la codifica per impostazione predefinita. L'assegnazione di input non attendibile a elementi DOM tramite funzioni di questo tipo può determinare attacchi tramite script da altri siti (XSS).| 

### <a name="example"></a>Esempio
Gli esempi seguenti non sono sicuri: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Non usare `innerHtml`. Usare invece `innerText`. Analogamente, usare `$("#elm").text()` invece di `$("#elm").html()`. 

## <a id="redirect-safe"></a>Convalidare come chiusi o sicuri tutti i reindirizzamenti nell'applicazione

| Title                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [The OAuth 2.0 Authorization Framework - Open Redirectors](http://tools.ietf.org/html/rfc6749#section-10.15) (Framework di autorizzazione di OAuth 2.0 - Redirector aperti) |
| **Passaggi** | <p>Se la progettazione dell'applicazione richiede il reindirizzamento a un percorso specificato dall'utente, è necessario vincolare le possibile destinazioni di reindirizzamento a un elenco "sicuro" predefinito di siti o domini. Tutti i reindirizzamenti nell'applicazione devono essere chiusi/sicuri.</p><p>A tale scopo, effettuare l'operazione seguente:</p><ul><li>Identificare tutti i reindirizzamenti.</li><li>Implementare una procedura di mitigazione appropriata per ogni reindirizzamento. Le procedure di mitigazione appropriate includono un elenco elementi consentiti di reindirizzamento o la conferma dell'utente. Se un servizio o un sito Web con una vulnerabilità causata da reindirizzamenti aperti usa i provider di identità Facebook/OAuth/OpenID, un utente malintenzionato può rubare il token di accesso di un utente e rappresentare tale utente. Si tratta di un rischio intrinseco quando si usa OAuth, come documentato nella sezione 10.15 relativa ai reindirizzamenti aperti della specifica RFC 6749 del framework di autorizzazione di OAuth 2.0. Analogamente, le credenziali degli utenti possono essere compromesse da attacchi di spear phishing con reindirizzamenti aperti.</li></ul>|

## <a id="string-method"></a>Implementare la convalida dell'input in un tutti i parametri di tipo stringa accettati dai metodi del controller

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Validating Model Data in an MVC Application](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) (Convalida dei dati del modello in un'applicazione MVC), [Principi guida per le applicazioni ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passaggi** | Per i metodi che accettano come argomento solo un tipo di dati primitivo e non modelli, deve essere eseguita la convalida dell'input con un'espressione regolare. In questo caso, è necessario usare Regex.IsMatch con un criterio regex valido. Se l'input non corrisponde all'espressione regolare specificata, il controllo dovrà essere interrotto e dovrà essere visualizzato un avviso relativo all'errore di convalida.| 

## <a id="dos-expression"></a>Impostare il limite massimo di timeout per l'elaborazione di espressioni regolari per impedire attacchi DoS causati da espressioni regolari errate

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, Web Form, MVC 5, MVC 6  |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Proprietà DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Passaggi** | Per prevenire attacchi Denial of Service contro espressioni regolari create in modo non corretto, che causano un elevato backtracking, impostare il timeout predefinito globale. Se il tempo necessario per l'elaborazione è superiore al limite massimo definito, verrà generata un'eccezione di timeout. In assenza di configurazione, il timeout sarà infinito.| 

### <a name="example"></a>Esempio
La configurazione seguente, ad esempio, genera un'eccezione RegexMatchTimeoutException se l'elaborazione richiede più di 5 secondi: 

```C#
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Evitare di usare Html.Raw nelle visualizzazioni Razor

| Title                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Passaggio | Le pagine Web ASP.Net (Razor) eseguono la codifica HTML automatica. A tutte le stringhe stampate da nugget di codice incorporati (blocchi @) viene applicata automaticamente la codifica HTML. Quando viene richiamato `HtmlHelper.Raw`, tuttavia, questo metodo restituisce markup senza codifica HTML. Se viene usato il metodo helper `Html.Raw()`, questo ignora la protezione con codifica automatica fornita da Razor.|

### <a name="example"></a>Esempio
L'esempio seguente non è sicuro: 

```C#
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Non usare `Html.Raw()` a meno che non sia necessario visualizzare markup. Questo metodo non esegue implicitamente la codifica dell'output. Usare altri helper ASP.NET, ad esempio `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Non usare query dinamiche nelle stored procedure

| Title                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Un attacco SQL injection sfrutta le vulnerabilità nella convalida dell'input per eseguire comandi arbitrari nel database. Può verificarsi quando l'applicazione usa l'input per costruire istruzioni SQL dinamiche per accedere al database, nonché se il codice usa stored procedure costituite da stringhe passate contenenti input utente non elaborato. Con l'attacco SQL injection, l'utente malintenzionato può eseguire comandi arbitrari nel database. Tutte le istruzioni SQL, incluse quelle nelle stored procedure, devono includere parametri. Le istruzioni SQL con parametri accetteranno senza problemi caratteri con un significato speciale per SQL (come la virgoletta singola) perché sono fortemente tipizzate. |

### <a name="example"></a>Esempio
L'esempio seguente è una stored procedure dinamica non sicura: 

```C#
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Esempio
Di seguito è riportata la stessa stored procedure implementata in modo sicuro: 
```C#
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Verificare l'esecuzione della convalida dei modelli nei metodi di API Web

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Model Validation in ASP.NET Web API ](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) (Convalida dei modelli in un'API Web ASP.NET) |
| **Passaggi** | Quando un client invia dati a un'API Web, è obbligatorio convalidare i dati prima di eseguire qualsiasi elaborazione. Per le API Web ASP.NET che accettano modelli come input, usare le annotazioni dei dati nei modelli per impostare le regole di convalida nelle proprietà dei modelli.|

### <a name="example"></a>Esempio
Il codice seguente illustra quanto descritto sopra: 

```C#
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Esempio
Nel metodo di azione dei controller dell'API la validità del modello deve essere selezionata in modo esplicito come illustrato di seguito: 

```C#
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Implementare la convalida dell'input in tutti i parametri di tipo stringa accettati dai metodi di API Web

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Validating Model Data in an MVC Application](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) (Convalida dei dati del modello in un'applicazione MVC), [Principi guida per le applicazioni ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passaggi** | Per i metodi che accettano come argomento solo un tipo di dati primitivo e non modelli, deve essere eseguita la convalida dell'input con un'espressione regolare. In questo caso, è necessario usare Regex.IsMatch con un criterio regex valido. Se l'input non corrisponde all'espressione regolare specificata, il controllo dovrà essere interrotto e dovrà essere visualizzato un avviso relativo all'errore di convalida.|

## <a id="typesafe-api"></a>Verificare che nell'API Web vengano usati parametri indipendenti dai tipi per l'accesso ai dati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Se si usa la raccolta Parameters, SQL considera l'input come un valore letterale anziché come codice eseguibile. La raccolta Parameters può essere usata per imporre vincoli di tipo e lunghezza sui dati di input. I valori non compresi nell'intervallo attivano un'eccezione. Se non vengono usati parametri SQL indipendenti dai tipi, gli utenti malintenzionati potrebbero eseguire attacchi di tipo injection incorporati nell'input non filtrato.</p><p>Quando si costruiscono query SQL, usare parametri indipendenti dai tipi per evitare gli attacchi SQL injection che possono verificarsi con input non filtrato. È possibile usare parametri indipendenti dai tipi con stored procedure e con istruzioni SQL dinamiche. I parametri vengono considerati dal database come valori letterali e non come codice eseguibile. Viene anche eseguito il controllo del tipo e della lunghezza dei parametri.</p>|

### <a name="example"></a>Esempio
Il codice seguente illustra come usare parametri indipendenti dai tipi con SqlParameterCollection quando si chiama una stored procedure. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Nell'esempio di codice precedente, la lunghezza del valore di input non può essere maggiore di 11 caratteri. Se i dati non sono conformi alla lunghezza o al tipo definito dal parametro, la classe SqlParameter genera un'eccezione. 

## <a id="sql-docdb"></a>Usare query SQL con parametri per Cosmos DB

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure DocumentDB | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Annuncio parametrizzazione SQL in Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Passaggi** | Sebbene Azure Cosmos DB supporta solo query di sola lettura, attacchi SQL injection è comunque possibile se le query vengono costruite mediante la concatenazione di input dell'utente. Un utente potrebbe ottenere l'accesso a dati a cui non dovrebbe accedere nella stessa raccolta creando query SQL dannose. Se le query vengono costruite in base all'input utente, usare query SQL con parametri. |

## <a id="schema-binding"></a>WCF: convalida dell'input tramite l'associazione allo schema

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, .NET Framework 3 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Passaggi** | <p>L'assenza di convalida espone a diversi attacchi di tipo injection.</p><p>La convalida dei messaggi rappresenta una linea di difesa nella protezione di un'applicazione WCF. Con questo approccio, si convalidano i messaggi usando schemi per proteggere le operazioni del servizio WCF dall'attacco di un client non autorizzato. Convalidare tutti i messaggi ricevuti dal client per proteggere il client dall'attacco di un servizio dannoso. In questo modo è possibile convalidare i messaggi quando le operazioni utilizzano contratti dati o contratti di messaggio, una funzionalità non supportata dalla convalida dei parametri. La convalida dei messaggi consente di creare la logica di convalida all'interno di schemi, offrendo così maggiore flessibilità e riducendo i tempi di sviluppo. Gli schemi possono essere riusati in diverse applicazioni all'interno dell'organizzazione, creando standard per la rappresentazione dei dati. La convalida dei messaggi consente anche di proteggere le operazioni quando utilizzano tipi di dati più complessi associati a contratti che rappresentano la logica di business.</p><p>Per eseguire la convalida dei messaggi, si compila prima di tutto uno schema che rappresenta le operazioni del servizio e i tipi di dati utilizzati da tali operazioni. Si crea quindi una classe .NET che implementa un controllo messaggio personalizzato del client e un controllo messaggio personalizzato dello strumento di recapito per convalidare i messaggi inviati al servizio o da esso ricevuti. Successivamente, si implementa un comportamento dell'endpoint personalizzato per abilitare la convalida dei messaggi sia nel client che nel servizio. Infine, si implementa un elemento di configurazione personalizzato per la classe che consente di esporre il comportamento dell'endpoint personalizzato esteso nel file di configurazione del servizio o del client.</p>|

## <a id="parameters"></a>WCF: convalida dell'input tramite controlli parametro

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, .NET Framework 3 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Passaggi** | <p>La convalida dell'input e dei dati rappresenta una linea di difesa importante nella protezione di un'applicazione WCF. È consigliabile convalidare tutti i parametri esposti nelle operazioni del servizio WCF per proteggere il servizio dall'attacco di un client malintenzionato. Viceversa, è consigliabile convalidare tutti i valori restituiti ricevuti dal client per proteggere il client dall'attacco di un servizio dannoso.</p><p>WCF offre diversi punti di estendibilità che consentono di personalizzare il comportamento del runtime WCF creando estensioni personalizzate. I controlli messaggio e i controlli parametro sono due meccanismi di estendibilità usati per ottenere maggiore controllo sui dati che vengono passati tra un client e un servizio. È consigliabile usare i controlli parametro per la convalida dell'input e i controlli messaggio solo quando è necessario controllare l'intero flusso dei messaggi da e verso un servizio.</p><p>Per eseguire la convalida dell'input, si compila una classe .NET e si implementa un controllo parametro personalizzato per convalidare i parametri per le operazioni nel servizio. Si implementa quindi un comportamento dell'endpoint personalizzato per abilitare la convalida sia nel client che nel servizio. Infine, si implementa un elemento di configurazione personalizzato per la classe che consente di esporre il comportamento dell'endpoint personalizzato esteso nel file di configurazione del servizio o del client.</p>|
