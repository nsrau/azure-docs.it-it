---
title: "Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni | Documentazione Microsoft"
description: "Questo argomento descrive le attività operative per il servizio di sincronizzazione Azure AD Connect e come prepararsi per il funzionamento di questo componente."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 4252a5ff3dd985185745060073cbe5432dba726a
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni
L'obiettivo di questo argomento è descrivere le attività operative per il servizio di sincronizzazione Azure AD Connect.

## <a name="staging-mode"></a>Modalità di gestione temporanea
La modalità di gestione temporanea può essere usata per diversi scenari, ad esempio:

* Disponibilità elevata.
* Testare e distribuire le nuove modifiche della configurazione.
* Introdurre un nuovo server e rimuovere quello vecchio.

Con un server in modalità di gestione temporanea è possibile apportare modifiche alla configurazione e visualizzarle in anteprima prima di attivare il server. È anche possibile eseguire operazioni di importazione e sincronizzazione complete per verificare che tutte le modifiche siano previste prima di introdurle nell'ambiente di produzione.

Durante l'installazione è possibile selezionare la **modalità di gestione temporanea**per il server. In questo modo il server sarà attivo per le operazioni di importazione e sincronizzazione, ma non eseguirà esportazioni. Un server in modalità di gestione temporanea non eseguirà la sincronizzazione o il writeback delle password, anche se queste funzionalità sono state selezionate durante l'installazione. Quando si disabilita la modalità di gestione temporanea, il server avvia l'esportazione e abilita la sincronizzazione e il writeback delle password.

> [!NOTE]
> Si supponga di avere Azure AD Connect con la funzionalità di sincronizzazione dell'hash delle password abilitata. Quando si abilita la modalità di gestione temporanea, il server arresta la sincronizzazione delle modifiche alla password da AD locale. Quando si disabilita la modalità di gestione temporanea, il server riepiloga la sincronizzazione delle modifiche alla password dal punto in cui è stata interrotta. Se il server viene lasciato in modalità di gestione temporanea per un lungo periodo di tempo, la sincronizzazione da parte del server di tutte le modifiche alle password, verificatesi durante un determinato periodo, potrebbe richiedere un po' di tempo.
>
>

È comunque possibile forzare un’esportazione utilizzando Synchronization Service Manager.

Un server in modalità di gestione temporanea continua a ricevere modifiche da Active Directory e Azure AD. Ha sempre una copia delle modifiche più recenti e può così assumere velocemente le funzioni di un altro server. Se si apportano modifiche alla configurazione del server primario, è necessario apportare le stesse modifiche al server in modalità di gestione temporanea.

Coloro che hanno una conoscenza delle tecnologie di sincronizzazione precedenti tengano presente che la modalità di gestione temporanea è diversa, perché il server ha il proprio database SQL. L'architettura consente al server in modalità di gestione temporanea di trovarsi in un altro data center.

### <a name="verify-the-configuration-of-a-server"></a>Verificare la configurazione di un server
Per applicare questo metodo, seguire questa procedura:

1. [Preparare](#prepare)
2. [Configurazione](#configuration)
3. [Importare e sincronizzare](#import-and-synchronize)
4. [Verificare](#verify)
5. [Cambiare il server attivo](#switch-active-server)

#### <a name="prepare"></a>Preparazione
1. Installare Azure AD Connect, selezionare **Modalità di gestione temporanea** e deselezionare **Avvia sincronizzazione** nell'ultima pagina dell'installazione guidata. In questo modo è possibile eseguire manualmente il motore di sincronizzazione.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Disconnettersi e accedere, quindi dal menu Start selezionare **Synchronization Service**(Servizio di sincronizzazione).

#### <a name="configuration"></a>Configurazione
Se si sono apportate modifiche personalizzate al server primario e si desidera confrontare la configurazione con server di gestione temporanea, usare l'[analizzatore di configurazione di Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importare e sincronizzare
1. Selezionare **Connettori** e quindi selezionare il primo connettore con il tipo **Servizi di dominio Active Directory**. Fare clic su **Run** (Esegui), selezionare **Full import** (Importazione completa) e fare clic su **OK**. Eseguire questi passaggi per tutti i connettori di questo tipo.
2. Selezionare il connettore con il tipo **Azure Active Directory (Microsoft)**. Fare clic su **Run** (Esegui), selezionare **Full import** (Importazione completa) e fare clic su **OK**.
3. Assicurarsi che la scheda Connectors (Connettori) sia ancora selezionata. Per ogni connettore con il tipo **Active Directory Domain Services** fare clic su **Run** (Esegui), selezionare **Delta Synchronization** (Sincronizzazione delta) e fare clic su **OK**.
4. Selezionare il connettore con il tipo **Azure Active Directory (Microsoft)**. Fare clic su **Run** (Esegui), selezionare **Delta Synchronization** (Sincronizzazione delta) e quindi fare clic su **OK**.

È stata eseguita l'esportazione delle modifiche in modalità di gestione temporanea in Azure AD e in AD locale (se si usa una distribuzione ibrida di Exchange). I passaggi successivi consentono di ispezionare quali sono gli elementi che stanno per essere modificati prima di avviare effettivamente l'esportazione nelle directory.

#### <a name="verify"></a>Verificare
1. Avviare un prompt dei comandi e passare a `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Eseguire: `csexport "Name of Connector" %temp%\export.xml /f:x` Il nome del connettore si trova nel servizio di sincronizzazione. Il nome sarà simile a "contoso.com - AAD" per Azure AD.
3. Eseguire: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Si avrà un file denominato export.csv in %temp%, che può essere esaminato in Microsoft Excel. Questo file contiene tutte le modifiche in fase di esportazione.
4. Apportare le modifiche necessarie ai dati o alla configurazione ed eseguire di nuovo questi passaggi (importazione, sincronizzazione e verifica) finché le modifiche che verranno esportate non saranno quelle previste.

**Informazioni sul file export.csv** La maggior parte del file è facilmente comprensibile. Ecco alcune abbreviazioni per comprendere il contenuto:
* OMODT: Object Modification Type. Indica se l'operazione a livello di oggetto è un'aggiunta, un aggiornamento o un'eliminazione.
* AMODT: Attribute Modification Type. Indica se l'operazione a livello di attributo è un'aggiunta, un aggiornamento o un'eliminazione.

**Recuperare gli identificatori comuni** Il file export.csv contiene tutte le modifiche che stanno per essere esportate. Ogni riga corrisponde a una modifica per un oggetto nello spazio connettore e l'oggetto è identificato dall'attributo DN. L'attributo DN è un identificatore univoco assegnato a un oggetto nello spazio connettore. Quando si dispone di molte righe/modifiche nel file export.csv da analizzare, potrebbe essere difficile individuare gli oggetti da modificare solo in base all'attributo DN. Per semplificare il processo di analisi delle modifiche, usare lo script di PowerShell csanalyzer.ps1. Lo script recupera gli identificatori comuni (ad esempio displayName e userPrincipalName) degli oggetti. Per usare lo script:
1. Copiare lo script di PowerShell dalla sezione [CSAnalyzer](#appendix-csanalyzer) in un file denominato `csanalyzer.ps1`.
2. Aprire una finestra di PowerShell e passare alla cartella in cui è stato creato lo script di PowerShell.
3. Eseguire: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. A questo punto si avrà un file denominato **processedusers1.csv**, che può essere esaminato in Microsoft Excel. Si noti che il file fornisce un mapping dall'attributo DN agli identificatori comuni (ad esempio displayName e userPrincipalName). Attualmente non include le modifiche effettive all'attributo che stanno per essere esportate.

#### <a name="switch-active-server"></a>Cambiare il server attivo
1. Disattivare il server attualmente attivo (DirSync/FIM/Azure AD Sync), in modo che non esegua l'esportazione in Azure AD oppure impostarlo in modalità di gestione temporanea (Azure AD Connect).
2. Eseguire l'installazione guidata nel server in **modalità di gestione temporanea** e disabilitare **questa modalità**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Ripristino di emergenza
Nell'ambito della progettazione dell'implementazione vengono pianificate le operazioni da eseguire nel caso di un'emergenza che causa la perdita del server di sincronizzazione. Ci sono diversi modelli da usare e quello scelto dipende da diversi fattori, ad esempio:

* Qual è la tolleranza per l'impossibilità di apportare modifiche agli oggetti in Azure AD durante il tempo di inattività?
* Se si usa la sincronizzazione delle password, gli utenti accettano di dover usare la vecchia password in Azure AD nel caso la modifichino in locale?
* Si ha una dipendenza dalle operazioni in tempo reale, ad esempio il writeback delle password?

A seconda delle risposte a queste domande e dei criteri dell'organizzazione, è possibile implementare una delle strategie seguenti:

* Ricompilare quando necessario.
* Avere un server di standby di riserva, ovvero in **modalità di gestione temporanea**.
* Usare macchine virtuali.

Se non si usa il database di SQL Express predefinito, è opportuno vedere anche la sezione [Disponibilità elevata di SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Ricompilare quando necessario
Una strategia valida consiste nel pianificare la ricompilazione di un server in caso di necessità. L'installazione del motore di sincronizzazione e l'esecuzione dell'importazione iniziale e della sincronizzazione possono essere generalmente completate in poche ore. Se non è disponibile un server di riserva, è possibile usare temporaneamente un controller di dominio per ospitare il motore di sincronizzazione.

Il server del motore di sincronizzazione non archivia lo stato degli oggetti, quindi il database può essere ricompilato dai dati disponibili in Active Directory e Azure AD. L'attributo **sourceAnchor** viene usato per aggiungere gli oggetti sia locali che dal cloud. Se si ricompila il server con gli oggetti esistenti in locale e nel cloud, il motore di sincronizzazione abbinerà di nuovo questi oggetti al momento della reinstallazione. Gli elementi che occorre documentare e salvare sono le modifiche della configurazione apportate al server, ad esempio le regole di filtro e sincronizzazione. Queste configurazioni personalizzate dovranno essere riapplicate prima di avviare la sincronizzazione.

### <a name="have-a-spare-standby-server---staging-mode"></a>Avere un server di standby di riserva, in modalità di gestione temporanea
Nel caso di un ambiente più complesso, è consigliabile avere uno o più server di standby. Durante l'installazione è possibile abilitare un server in **modalità di gestione temporanea**.

Per altre informazioni, vedere le [modalità di gestione temporanea](#staging-mode).

### <a name="use-virtual-machines"></a>Usare macchine virtuali
Un metodo comune e supportato consiste nell'eseguire il motore di sincronizzazione in una macchina virtuale. Nel caso di un problema dell'host, è possibile eseguire la migrazione dell'immagine del server del motore di sincronizzazione in un altro server.

### <a name="sql-high-availability"></a>Disponibilità elevata di SQL
Se non si usa SQL Server Express fornito con Azure AD Connect, è necessario prendere in considerazione anche la disponibilità elevata per SQL Server. Le soluzioni a disponibilità elevata supportate includono il clustering di SQL e AOA (Gruppi di disponibilità Always On), mentre il mirroring è una delle soluzioni non supportate.

In Azure AD Connect versione 1.1.524.0 è stato aggiunto il supporto per SQL AOA. È necessario abilitare SQL AOA prima di installare Azure AD Connect. Durante l'installazione, Azure AD Connect rileva se l'istanza SQL specificata è abilitata per SQL AOA oppure no. Se SQL AOA è abilitato, Azure AD Connect rileva quindi se SQL AOA è configurato per usare la replica sincrona o asincrona. Quando si configura il listener del gruppo di disponibilità, è consigliabile impostare la proprietà RegisterAllProvidersIP su 0. Ciò perché Azure AD Connect usa il client nativo di SQL per connettersi a SQL e il client nativo SQL non supporta l'uso della proprietà MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Appendice CSAnalyzer
Vedere la sezione [Verificare](#verify) su come usare questo script.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**  

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)  
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)  
