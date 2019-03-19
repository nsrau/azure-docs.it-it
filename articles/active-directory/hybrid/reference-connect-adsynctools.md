---
title: 'Azure AD Connect: Informazioni di riferimento su PowerShell ADSyncTools | Microsoft Docs'
description: Questo documento fornisce informazioni di riferimento sul modulo di PowerShell ADSyncTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443335"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  Informazioni di riferimento su PowerShell ADSyncTools
La documentazione seguente fornisce informazioni di riferimento sul modulo di PowerShell ADSyncTools.psm1 incluso in Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>RIEPILOGO
Cancella mS-Ds-ConsistencyGuid dall'utente di AD

### <a name="syntax"></a>SINTASSI

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Cancella il valore in mS-Ds-ConsistencyGuid per l'utente di AD di destinazione

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-user"></a>-User
Utente di destinazione in AD da impostare

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>RIEPILOGO
{{Compilare il riepilogo}}

### <a name="syntax"></a>SINTASSI

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>DESCRIZIONE
{{Compilare la descrizione}}

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>Esempio 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Aggiungere qui la descrizione dell'esempio}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>RIEPILOGO
{{Compilare il riepilogo}}

### <a name="syntax"></a>SINTASSI

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
{{Compilare la descrizione}}

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>Esempio 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Aggiungere qui la descrizione dell'esempio}}

### <a name="parameters"></a>PARAMETRI

#### <a name="-database"></a>-Database
{{Compilare la descrizione di Database}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{Compilare la descrizione di Istance}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Compilare la descrizione di Password}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Compilare la descrizione di Server}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Compilare la descrizione di UserName}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>RIEPILOGO
Esporta il report ConsistencyGuid

### <a name="syntax"></a>SINTASSI

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Genera un report ConsistencyGuid basato su un file di importazione CSV da Import-ADSyncToolsImmutableIdMigration

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Usare l'ID di accesso alternativo (posta elettronica)

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nome del file di output per i file CSV e LOG

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>RIEPILOGO
{{Compilare il riepilogo}}

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>DESCRIZIONE
{{Compilare la descrizione}}

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>Esempio 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Aggiungere qui la descrizione dell'esempio}}

### <a name="parameters"></a>PARAMETRI

#### <a name="-hostname"></a>-hostName
{{Compilare la descrizione di hostName}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>RIEPILOGO
Ottiene l'utente da AD

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Restituisce un oggetto di AD: supporto di più foreste

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-user"></a>-User
Utente di destinazione in AD per impostare ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>RIEPILOGO
Ottiene mS-Ds-ConsistencyGuid dall'utente di AD

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Restituisce il valore nell'attributo mS-Ds-ConsistencyGuid dell'utente di AD di destinazione in formato GUID

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-user"></a>-User
Utente di destinazione in AD da impostare

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>RIEPILOGO
Ottiene ObjectGuid dall'utente di AD

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Restituisce il valore nell'attributo ObjectGUID dell'utente di AD di destinazione in formato GUID

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-user"></a>-User
Utente di destinazione in AD da impostare

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>RIEPILOGO
Ottiene la cronologia di esecuzione di AAD Connect

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Funzione che restituisce la cronologia di esecuzione di AAD Connect in formato XML

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>ESEMPIO 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-days"></a>-Days
{{Compilare la descrizione di Days}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>RIEPILOGO
Ottiene gli utenti con errori di SourceAnchor modificato

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Esegue una query sulla cronologia di esecuzione di AAD Connect ed esporta tutti gli utenti per cui è segnalato l'errore: "SourceAnchor attribute has changed." (L'attributo SourceAnchor è stato modificato)

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Enter your log file path with file name" #"\<Source_Path\>" $outputPath = Read-Host -Prompt "Enter your out file path with file name" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-sourcepath"></a>-sourcePath
{{Compilare la descrizione di sourcePath}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Compilare la descrizione di outputPath}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>RIEPILOGO
Importa ImmutableID da AAD

### <a name="syntax"></a>SINTASSI

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Genera un file con tutti gli utenti sincronizzati di Azure AD contenente il valore ImmutableID in formato GUID. Requisiti: modulo di PowerShell MSOnline

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-output"></a>-Output
File di output CSV

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Recupera gli utenti sincronizzati dal Cestino di Azure AD

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>RIEPILOGO
{{Compilare il riepilogo}}

### <a name="syntax"></a>SINTASSI

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
{{Compilare la descrizione}}

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>Esempio 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Aggiungere qui la descrizione dell'esempio}}

### <a name="parameters"></a>PARAMETRI

#### <a name="-query"></a>-Query
{{Compilare la descrizione di Query}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Compilare la descrizione di SqlConnection}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>RIEPILOGO
Script per rimuovere i certificati scaduti dall'attributo UserCertificate

### <a name="syntax"></a>SINTASSI

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Questo script accetta tutti gli oggetti da un'unità organizzativa di destinazione nel dominio di Active Directory filtrati in base alla classe dell'oggetto (utente/computer) ed elimina tutti i certificati scaduti presenti nell'attributo UserCertificate.
Per impostazione predefinita (modalità BackupOnly) verrà solo eseguito il backup dei certificati scaduti in un file e non verranno apportate modifiche in AD.
Se si usa -BackupOnly $false, qualsiasi certificato scaduto presente nell'attributo UserCertificate per questi oggetti scaduti verrà rimosso da AD dopo la copia nel file.
Verrà eseguito il backup di ogni certificato in un file separato: ObjectClass_ObjectGUID_CertThumprint.cer. Lo script creerà anche un file di log in formato CSV che mostra tutti gli utenti con certificati validi o scaduti e l'azione intrapresa (ignorato/esportato/eliminato).

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>ESEMPIO 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARAMETRI

#### <a name="-targetou"></a>-TargetOU
Unità organizzativa di destinazione per la ricerca degli oggetti di AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly non eliminerà alcun certificato da AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Filtro della classe di oggetto

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>RIEPILOGO
Breve descrizione

### <a name="syntax"></a>SINTASSI

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>DESCRIZIONE
Descrizione lunga

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>RIEPILOGO
{{Compilare il riepilogo}}

### <a name="syntax"></a>SINTASSI

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>DESCRIZIONE
{{Compilare la descrizione}}

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>Esempio 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Aggiungere qui la descrizione dell'esempio}}

### <a name="parameters"></a>PARAMETRI

#### <a name="-hostname"></a>-hostName
{{Compilare la descrizione di hostName}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>RIEPILOGO
(PER ESEGUIRE) Ripristina l'attributo AD UserCertificate da un file di certificato

### <a name="syntax"></a>SINTASSI

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>DESCRIZIONE
Descrizione lunga

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>RIEPILOGO
Imposta mS-Ds-ConsistencyGuid per un utente di AD

### <a name="syntax"></a>SINTASSI

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Imposta un valore nell'attributo mS-Ds-ConsistencyGuid per l'utente di AD di destinazione

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-user"></a>-User
Utente di destinazione in AD per impostare ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId (matrice di byte, GUID, stringa GUID o stringa Base64)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>RIEPILOGO
{{Compilare il riepilogo}}

### <a name="syntax"></a>SINTASSI

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>DESCRIZIONE
{{Compilare la descrizione}}

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>Esempio 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Aggiungere qui la descrizione dell'esempio}}

### <a name="parameters"></a>PARAMETRI

#### <a name="-hostname"></a>-hostName
{{Compilare la descrizione di hostName}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Compilare la descrizione di port}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>RIEPILOGO
Crea un file di traccia da un passaggio AD Import

### <a name="syntax"></a>SINTASSI

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Analizza tutte le query LDAP di un'esecuzione di AAD Connect AD Import da un checkpoint specificato di limite AD (cookie partizione). Crea un file di traccia '.\ADimportTrace_aaaaMMggHHmmss.log' nella cartella corrente.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Compilare la descrizione di ADConnectorXML}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
File XML di esportazione di AD Connector

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Controller di dominio di destinazione

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
Nome distinto della radice della foresta

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Tipi di oggetti di AD da analizzare \> * = tutti i tipi di oggetto

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Se è già in esecuzione come amministratore di dominio, non è necessario fornire le credenziali di AD.
Input manuale della filigrana, invece del file XML, ad esempio $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>RIEPILOGO
Breve descrizione

### <a name="syntax"></a>SINTASSI

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Descrizione lunga

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ESEMPIO 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-context"></a>-Context
Descrizione della Guida di param1

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Descrizione della Guida di param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Descrizione della Guida di param2

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Descrizione della Guida di param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>RIEPILOGO
Aggiorna gli utenti con il nuovo ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SINTASSI

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Aggiorna gli utenti con il nuovo valore ConsistencyGuid (ImmutableId) recuperato dal report ConsistencyGuid. Questa funzione supporta l'opzione WhatIf. Nota: il report ConsistencyGuid deve essere importato con il delimitatore TAB

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>ESEMPIO 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Azione

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nome del file di output per i file di LOG

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Mostra l'esito in caso di esecuzione del cmdlet.
Il cmdlet non viene eseguito.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Richiede la conferma dell'utente prima di eseguire il cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
