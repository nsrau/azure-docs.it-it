

## <a name="multi-and-single-instance-vms"></a>VM a istanza singola e a istanza multipla
Per molti clienti che svolgono le proprie attività in Azure è fondamentale essere in grado di pianificare la manutenzione delle proprie VM (Virtual Machine, macchina virtuale), poiché questa comporta circa 15 minuti di inattività. Per controllare il momento in cui le VM vengono sottoposte a manutenzione pianificata è possibile sfruttare set di disponibilità.

Per le VM in esecuzione in Azure sono possibili due configurazioni: a istanza singola o a istanza multipla. Le VM presenti in un set di disponibilità sono configurate a istanza multipla. Si noti che anche VM a istanza singola possono essere distribuite in un set di disponibilità e considerate come macchine virtuali a istanza multipla. Le VM NON presenti in un set di disponibilità sono configurate come macchine virtuali a istanza singola.  Per informazioni dettagliate sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali Windows](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Gestire la disponibilità delle macchine virtuali Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Gli aggiornamenti di manutenzione pianificata delle VM a istanza singola avvengono separatamente da quelli delle VM a istanza multipla. Riconfigurando le VM a istanza multipla come macchine virtuali a istanza singola o viceversa è possibile controllare quando sottoporre le VM alla manutenzione pianificata. Per informazioni dettagliate sulla manutenzione pianificata delle macchine virtuali di Azure, vedere [Manutenzione pianificata per macchine virtuali Linux in Azure](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [Manutenzione pianificata per macchine virtuali Windows in Azure](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="for-multi-instance-configuration"></a>Configurazione a istanza multipla
È possibile selezionare l'ora della manutenzione pianificata delle VM distribuite in una configurazione di set di disponibilità rimuovendo queste VM dal set di disponibilità corrispondente.

1. Per le VM in una configurazione a istanza multipla viene inviato un messaggio di posta elettronica sette giorni prima della manutenzione pianificata. Nel messaggio sono inclusi gli ID sottoscrizione e i nomi delle VM a istanza multipla interessate.
2. Durante questi sette giorni è possibile scegliere l'ora dell'aggiornamento delle istanze rimuovendo le VM a istanza multipla dell'area interessata dal set di disponibilità. Questa modifica di configurazione comporta un riavvio, dato che la macchina virtuale viene spostata da un host fisico destinato alla manutenzione a un altro host fisico non destinato alla manutenzione. 
3. Per rimuovere una VM dal relativo set di disponibilità è possibile usare il portale classico. 
   
   1. Nel portale classico, fare clic sulla VM, quindi selezionare "configurazione". 
   2. Nella sezione "impostazioni", è possibile visualizzare il set di disponibilità in cui si trova la VM.
      
      ![Selezione del set di disponibilità](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)
   3. Nel menu a discesa per l'impostazione della disponibilità selezionare "Rimuovi dal set di disponibilità".
      
      ![Rimuovi dal set](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)
   4. Nella parte inferiore selezionare "Salva". Selezionare "Sì" per confermare il riavvio della VM tramite questa azione.
4. Queste VM verranno spostate in host a istanza singola e non verranno aggiornate durante la manutenzione pianificata per le configurazioni di set di disponibilità.
5. Al termine dell'aggiornamento delle VM dei set di disponibilità in base alla pianificazione descritta nel messaggio di posta elettronica originale, è necessario aggiungere di nuovo le VM ai relativi set di disponibilità. Queste VM verranno nuovamente configurate come macchine virtuali a istanza multipla. Dopo la modifica di configurazione delle VM da istanza singola a istanza multipla è necessario il riavvio. In genere, dopo il completamento di tutti gli aggiornamenti a istanza multipla nell'intero ambiente Azure, viene eseguita la manutenzione delle macchine virtuali a istanza singola.

Si noti che è possibile ottenere questo risultato anche tramite Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Configurazione a istanza singola
È possibile selezionare l'ora della manutenzione pianificata delle VM in una configurazione a istanza singola aggiungendo queste VM a set di disponibilità.

Procedura dettagliata

1. Per le VM in una configurazione a istanza singola viene inviato un messaggio di posta elettronica sette giorni prima della manutenzione pianificata. Nel messaggio sono inclusi gli ID sottoscrizione e i nomi delle VM a istanza singola interessate. 
2. Durante questi sette giorni è possibile scegliere l'ora del riavvio dell'istanza spostando le VM a istanza singola in un set di disponibilità nella stessa area. Questa modifica di configurazione comporta un riavvio, dato che la macchina virtuale viene spostata da un host fisico destinato alla manutenzione a un altro host fisico non destinato alla manutenzione.
3. Seguire le istruzioni riportate qui per aggiungere VM esistenti a set di disponibilità tramite il portale classico e Azure PowerShell. Vedere l'esempio relativo ad Azure PowerShell nella nota più avanti.
4. Dopo la riconfigurazione come VM a istanza multipla, le VM vengono escluse dalla manutenzione pianificata per le macchine virtuali a istanza singola.
5. Al termine dell'aggiornamento delle VM a istanza singola in base alla pianificazione descritta nel messaggio di posta elettronica originale, è possibile rimuovere le VM dai relativi set di disponibilità. Queste VM verranno nuovamente configurate come macchine virtuali a istanza singola.

Si noti che è possibile ottenere questo risultato anche tramite Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Gestione della disponibilità delle macchine virtuali]: virtual-machines-windows-tutorial.md
[confronto tra manutenzione pianificata e non pianificata]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/


<!--HONumber=Nov16_HO3-->


