---
title: 'Azure AD Connect: informazioni di riferimento su PowerShell ADSyncConfig | Microsoft Docs'
description: Questo documento fornisce informazioni di riferimento sul modulo di PowerShell ADSyncConfig.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381196"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  informazioni di riferimento sul modulo di PowerShell ADSyncConfig
La documentazione seguente fornisce informazioni di riferimento sul modulo di PowerShell ADSyncConfig.psm1 incluso in Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>RIEPILOGO
Ottiene il dominio e il nome dell'account configurato in ogni connettore AD

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>DESCRIZIONE
Questa funzione usa il cmdlet "Get-ADSyncConnector" presente in AAD Connect per recuperare dai parametri di connettività una tabella che mostra l'account del connettore o dei connettori AD.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>RIEPILOGO
Ottiene gli oggetti di AD con l'ereditarietà delle autorizzazioni disabilitata

### <a name="syntax"></a>SINTASSI

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Esegue una ricerca in AD a partire dal parametro SearchBase e restituisce tutti gli oggetti, filtrati in base al parametro ObjectClass, in cui l'ereditarietà dell'elenco di controllo di accesso è attualmente disabilitata.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>ESEMPIO 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>ESEMPIO 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMETRI

#### <a name="-searchbase"></a>-SearchBase
Base di ricerca della query LDAP, che può essere un nome distinto del dominio AD o un nome di dominio completo

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

#### <a name="-objectclass"></a>-ObjectClass
Classe degli oggetti in cui eseguire la ricerca, che può essere "*" (per qualsiasi classe di oggetti), "utente", "gruppo", "contenitore" e così via. Per impostazione predefinita, questa funzione cerca la classe di oggetti "organizationalUnit".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per le autorizzazioni di lettura di base.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncBasicReadPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti computer discendenti
2.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti dispositivo discendenti
3.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti foreignsecurityprincipal discendenti
5.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti utente discendenti
6.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti inetorgperson discendenti
7.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti gruppo discendenti
8.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti contatto discendenti

Queste autorizzazioni vengono applicate a tutti i domini nella foresta.
Facoltativamente, è possibile specificare un DistinguishedName nel parametro ADobjectDN per impostare queste autorizzazioni solo sullo specifico oggetto AD (inclusa l'ereditarietà agli oggetti secondari).

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>ESEMPIO 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>ESEMPIO 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Nome distinto dell'oggetto AD di destinazione per l'impostazione delle autorizzazioni (facoltativo)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parametro facoltativo che indica se il contenitore AdminSDHolder non deve essere aggiornato con queste autorizzazioni

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per la funzionalità di distribuzione ibrida Exchange.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncExchangeHybridPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Accesso alle proprietà in lettura/scrittura su tutti gli attributi per tutti gli oggetti utente discendenti
2.
Accesso alle proprietà in lettura/scrittura su tutti gli attributi per tutti gli oggetti inetorgperson discendenti
3.
Accesso alle proprietà in lettura/scrittura su tutti gli attributi per tutti gli oggetti gruppo discendenti
4.
Accesso alle proprietà in lettura/scrittura su tutti gli attributi per tutti gli oggetti contatto discendenti

Queste autorizzazioni vengono applicate a tutti i domini nella foresta.
Facoltativamente, è possibile specificare un DistinguishedName nel parametro ADobjectDN per impostare queste autorizzazioni solo sullo specifico oggetto AD (inclusa l'ereditarietà agli oggetti secondari).

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>ESEMPIO 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>ESEMPIO 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Nome distinto dell'oggetto AD di destinazione per l'impostazione delle autorizzazioni (facoltativo)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parametro facoltativo che indica se il contenitore AdminSDHolder non deve essere aggiornato con queste autorizzazioni

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per la funzionalità Cartelle pubbliche della posta di Exchange.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncExchangeMailPublicFolderPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Accesso alle proprietà in lettura su tutti gli attributi per tutti gli oggetti publicfolder discendenti

Queste autorizzazioni vengono applicate a tutti i domini nella foresta.
Facoltativamente, è possibile specificare un DistinguishedName nel parametro ADobjectDN per impostare queste autorizzazioni solo sullo specifico oggetto AD (inclusa l'ereditarietà agli oggetti secondari).

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>ESEMPIO 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>ESEMPIO 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Nome distinto dell'oggetto AD di destinazione per l'impostazione delle autorizzazioni (facoltativo)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parametro facoltativo che indica se il contenitore AdminSDHolder non deve essere aggiornato con queste autorizzazioni

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per la funzionalità mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncMsDsConsistencyGuidPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Accesso alle proprietà in lettura/scrittura sull'attributo mS-DS-ConsistencyGuid per tutti gli oggetti utente discendenti

Queste autorizzazioni vengono applicate a tutti i domini nella foresta.
Facoltativamente, è possibile specificare un DistinguishedName nel parametro ADobjectDN per impostare queste autorizzazioni solo sullo specifico oggetto AD (inclusa l'ereditarietà agli oggetti secondari).

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>ESEMPIO 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>ESEMPIO 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Nome distinto dell'oggetto AD di destinazione per l'impostazione delle autorizzazioni (facoltativo)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parametro facoltativo che indica se il contenitore AdminSDHolder non deve essere aggiornato con queste autorizzazioni

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per la sincronizzazione dell'hash delle password.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncPasswordHashSyncPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Replica modifiche directory
2.
Replica di tutte le modifiche directory

Queste autorizzazioni vengono fornite a tutti i domini nella foresta.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per il writeback delle password da Azure AD.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncPasswordWritebackPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Reimpostazione della password sugli oggetti utente discendenti
2.
Accesso alle proprietà in scrittura sull'attributo lockoutTime per tutti gli oggetti utente discendenti
3.
Accesso alle proprietà in scrittura sull'attributo pwdLastSet per tutti gli oggetti utente discendenti

Queste autorizzazioni vengono applicate a tutti i domini nella foresta.
Facoltativamente, è possibile specificare un DistinguishedName nel parametro ADobjectDN per impostare queste autorizzazioni solo sullo specifico oggetto AD (inclusa l'ereditarietà agli oggetti secondari).

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>ESEMPIO 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>ESEMPIO 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Nome distinto dell'oggetto AD di destinazione per l'impostazione delle autorizzazioni (facoltativo)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parametro facoltativo che indica se il contenitore AdminSDHolder non deve essere aggiornato con queste autorizzazioni

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>RIEPILOGO
Limita le autorizzazioni su un oggetto AD non altrimenti incluso in alcun gruppo di sicurezza protetto di AD.
Un esempio tipico è l'account AD Connect (MSOL) creato automaticamente da AAD Connect.
Questo account ha autorizzazioni di replica su tutti i domini, ma può essere facilmente compromesso in quanto non è protetto.

### <a name="syntax"></a>SINTASSI

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncRestrictedPermissions limita le autorizzazioni sull'account fornito.
La limitazione delle autorizzazioni prevede i passaggi seguenti:
1.
Disabilitare l'ereditarietà nell'oggetto specificato
2.
Rimuovere tutte le voci ACE nell'oggetto specifico, ad eccezione delle voci ACE specifiche di SELF.
Le autorizzazioni predefinite devono rimanere inalterate per SELF.
3.
Assegnare le autorizzazioni specifiche seguenti:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory di cui occorre limitare le autorizzazioni.
Si tratta in genere dell'account MSOL_nnnnnnnnnn o di un account personalizzato configurato nel connettore AD.

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

#### <a name="-credential"></a>-Credential
Credenziali amministrative con i privilegi necessari per limitare le autorizzazioni per l'account ADConnectorAccountDN. Si tratta in genere dell'amministratore di dominio o dell'organizzazione. Usare il nome di dominio completo dell'account amministratore per evitare errori di ricerca dell'account.
Esempio: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
Quando si usa DisableCredentialValidation, la funzione non verifica se le credenziali fornite in -Credential sono valide in AD e se l'account specificato ha i privilegi necessari per limitare le autorizzazioni sull'account ADConnectorAccountDN.

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>RIEPILOGO
Inizializza la foresta e il dominio di Active Directory per il writeback dei gruppi da Azure AD.

### <a name="syntax"></a>SINTASSI

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
La funzione Set-ADSyncUnifiedGroupWritebackPermissions fornisce all'account di sincronizzazione di AD le autorizzazioni necessarie, che includono:
1.
Autorizzazioni generiche di lettura/scrittura, eliminazione, eliminazione albero e creazione/eliminazione degli oggetti figlio per tutti i tipi di oggetto gruppo e gli oggetti secondari

Queste autorizzazioni vengono applicate a tutti i domini nella foresta.
Facoltativamente, è possibile specificare un DistinguishedName nel parametro ADobjectDN per impostare queste autorizzazioni solo sullo specifico oggetto AD (inclusa l'ereditarietà agli oggetti secondari).
In questo caso, ADobjectDN sarà il nome distinto del contenitore che si vuole collegare alla funzionalità di writeback gruppi.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>ESEMPIO 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>ESEMPIO 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>ESEMPIO 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nome dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Dominio dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nome distinto dell'account Active Directory che viene o verrà usato dal servizio di sincronizzazione Azure AD Connect per gestire gli oggetti nella directory.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Nome distinto dell'oggetto AD di destinazione per l'impostazione delle autorizzazioni (facoltativo)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Parametro facoltativo che indica se il contenitore AdminSDHolder non deve essere aggiornato con queste autorizzazioni

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
Richiede conferma prima di eseguire il cmdlet.

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>RIEPILOGO
Mostra le autorizzazioni di un oggetto AD specificato.

### <a name="syntax"></a>SINTASSI

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE
Questa funzione restituisce tutte le autorizzazioni AD attualmente impostate per un determinato oggetto AD nel parametro -ADobjectDN.
ADobjectDN deve essere specificato in un formato DistinguishedName.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRI

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN Description}}

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

#### <a name="commonparameters"></a>CommonParameters
Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
