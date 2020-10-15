# Änderung

## Beschreibung

Dient zur Erfassung von Änderungen an anderen Entitäten.

## Eigenschaften

| Bezeichnung (de) | Bezeichnung (en) | Typ  | Beschreibung  |
| ---------------- | ---------------- | ---- | ------------- |
| Eintrags-ID | Entry ID | Integer | Die eindeutige ID der Abteilung |
| Änderungstyp | Change type | String(10) | Gibt an, welche Art von Änderung vorliegt |
| Entitätstyp | Entity type | String(100) | Gibt an, welchen Typ (Klasse) die geänderte Entität hat |
| Entitäts-Record-ID | Entity record ID | Guid | Die Record-ID der geänderten Entität |
| Eigenschaftsname | Property name | String(75) | Der Name der Eigenschaft, die geändert wurde |
| Alter Wert | Old value | (JSON-)String(max) | Der alte Wert der geänderten Eigenschaft bzw. der Wert, der gelöscht wurde |
| Neuer Wert | New value | (JSON-)String(max) | Der neue Wert der geänderten Eigenschaft bzw der Wert, der hinzugefügt wurde |
| Geändert am | Changed at | DateTime | Der Zeitpunkt, an dem die Änderung durchgeführt wurde |
| Geändert von | Changed by | String(75) | Der Name des Benutzers, der die Änderung durchgeführt hat |

## Implementierungen

!!! note ""
    Die unten aufgeführten Code-Ausschnitte sind u. U. gekürzt. Die tatsächlichen Implementierungen können umfangreicher sein!

### Datenbank

```sql
CREATE TABLE [dbo].[Aenderung](
	[EintragID] [int] IDENTITY(1,1) NOT NULL,
	[Typ] [nvarchar](10) NOT NULL,
	[Entitaetstyp] [nvarchar](100) NOT NULL,
	[EntitaetRecordID] [uniqueidentifier] NOT NULL,
	[EntitaetEigenschaft] [nvarchar](75) NULL,
	[WertAlt] [nvarchar](max) NULL,
	[WertNeu] [nvarchar](max) NULL,
	[GeaendertAm] [datetime2](7) NOT NULL,
	[GeaendertVon] [nvarchar](75) NOT NULL,
 CONSTRAINT [PK_Aenderung] PRIMARY KEY CLUSTERED 
(
	[EintragID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

### Anwendung

```csharp
public class ChangelogEntry
{
    public int EntryId { get; set; }

    public string ChangeType { get; set; }

    public string EntityType { get; set; }

    public Guid RecordId { get; set; }

    public string PropertyName { get; set; }

    public string OldValue { get; set; }

    public string NewValue { get; set; }

    public DateTime ChangedAt { get; set; }

    public string ChangedBy { get; set; }
}
```

### Transfer

```js
{
    "entryId": 49,
    "changeType": "Update",
    "entityType": "Probat.Pitamy.Shared.Models.Hardware",
    "recordId": "b96595a9-0ed8-4d43-b81b-f65c7e583430",
    "propertyName": "HardwareNo",
    "oldValue": "3",
    "newValue": "1",
    "changedAt": "2020-05-21T22:44:48.7617691Z",
    "changedBy": "Philip"
}
```