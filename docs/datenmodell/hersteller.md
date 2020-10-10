# Hersteller

## Beschreibung

Hersteller dienen dazu, Hard- und Software, die von der gleichen Firma produziert wurden, zuverlässig zusammenfassen zu können.

## Eigenschaften

|  Bezeichnung (de) | Bezeichnung (en) | Typ  | Beschreibung  |
|  :------------    | :---------       | :----| :----         |
|  **Hersteller-Name** | Manufacturer name |  String(50) |  Die Bezeichnung des Herstellers. Muss einzigartig sein! |

## Implementierungen

!!! note ""
    Die unten aufgeführten Code-Ausschnitte sind u. U. gekürzt. Die tatsächlichen Implementierungen können umfangreicher sein!

### Datenbank

```sql
CREATE TABLE [dbo].[Hersteller](
	[Herstellername] [nvarchar](50) NOT NULL,
	[ErstelltAm] [datetime2](7) NOT NULL,
	[ErstelltVon] [nvarchar](75) NOT NULL,
	[GeaendertAm] [datetime2](7) NULL,
	[GeaendertVon] [nvarchar](75) NULL,
	[RecordID] [uniqueidentifier] NOT NULL,
 CONSTRAINT [PK_Hersteller] PRIMARY KEY CLUSTERED 
(
	[Herstellername] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
```

### Anwendung

```csharp
public class Manufacturer : ITrackable
{
	public const int ManufacturerNameMaxLength = 50;

	public string ManufacturerName
	{
		get;
		set;
	}

	public List<HardwareModel> Models
	{
		get;
		set;
	}

	public DateTime CreatedAt
	{
		get;
		set;
	}

	public string CreatedBy
	{
		get;
		set;
	}

	public DateTime? LastUpdatedAt
	{
		get;
		set;
	}

	public string LastUpdatedBy
	{
		get;
		set;
	}

	public Guid RecordId
	{
		get;
		set;
	}

	public override string ToString()
	{
		return ManufacturerName;
	}

	public override int GetHashCode()
	{
		if (ManufacturerName == null)
		{
			return 0;
		}
		else
		{
			return ManufacturerName.GetHashCode();
		}
	}

	public override bool Equals(object obj)
	{
		if (obj == null)
		{
			return false;
		}

		Manufacturer otherManufacturer = (obj as Manufacturer);

		if (otherManufacturer == null)
		{
			return false;
		}

		if (this.ManufacturerName.Equals(otherManufacturer.ManufacturerName))
		{
			return true;
		}
		else
		{
			return false;
		}
	}
}
```

### Transfer

```js
{
        "manufacturerName": "Microsoft",
        "models": null,
        "createdAt": "2020-02-16T20:42:12.2276411Z",
        "createdBy": "Philip",
        "lastUpdatedAt": null,
        "lastUpdatedBy": null,
        "recordId": "803f7754-0156-4b93-9dd1-e1e6512fc7bd"
}
```