# Indirizzo IP interno statico

Le macchine virtuali IaaS e le istanze del ruolo PaaS in una rete virtuale ricevono automaticamente un indirizzo IP interno da un intervallo specificato. Tale indirizzo viene mantenuto per le macchine virtuali e le istanze del ruolo, fino a quando non vengono rimosse. Disabilitare un'istanza di macchina virtuale o un ruolo interrompendolo da PowerShell, CLI Azure o dal portale di Azure. In questi casi, una volta che l'istanza di macchina virtuale o del ruolo viene riavviata, riceverà un indirizzo IP disponibile dall'infrastruttura di Azure, che potrebbe non essere lo stesso che aveva in precedenza. In alcuni casi, non si desidera che una macchina virtuale o istanza del ruolo disponga di un indirizzo IP dinamico, ad esempio, se la macchina virtuale eseguirà DNS o sarà un controller di dominio.

>[AZURE.NOTE]Un indirizzo IP interno dinamico rimane associato alla macchina virtuale se si arresta la macchina virtuale dal sistema operativo guest. Le macchine virtuali vengono disattivate solo quando arrestate dal portale di Azure, PowerShell o Azure CLI.

<!---HONumber=Sept15_HO2-->