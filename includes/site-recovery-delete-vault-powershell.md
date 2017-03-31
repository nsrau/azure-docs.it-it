## <a name="delete-a-recovery-services-vault-powershell"></a>Eliminare un insieme di credenziali di Servizi di ripristino (PowerShell)

1. Ottenere l'insieme di credenziali di Servizi di ripristino

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Eliminare l'insieme di credenziali

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Usare il comando precedente comando con massima cautela, poiché se si elimina accidentalmente un insieme di credenziali, si perderanno tutti i dati. Si tratta di un'operazione permanente e non è possibile annullarla.  


