# Abteilung

## Beschreibung

Abteilung/Organisationseinheit, der Mitarbeiter zugeordnet werden können.

## Eigenschaften

|  Bezeichnung (de) | Bezeichnung (en) | Typ  | Beschreibung  |
| ----------------- | ---------------- | ---- | ------------- |
| Abteilungs-ID | Department ID | Integer | Die eindeutige ID der Abteilung. |
| Standortkürzel | Location code | String([...](../standort)) | Der Standort, dem die Abteilung zugeordnet ist. |
| Abteilungskürzel | Department code | String(10) | Das Kürzel der Abteilung. Muss in Kombination mit dem Standort eindeutig sein. |
| Abteilungsname | Department name | String(50) | Die Bezeichnung der Abteilung. |
| Kostenstelle | Cost center | String(10) | Die Kostenstelle, der die Abteilung zugeordnet ist. |
| Übergeordnete Abteilung (ID) | Superordinate Department (ID) | Integer | Die übergeordnete Abteilung, der diese Abteilung zugeordnet ist. |

## Implementierungen

!!! note ""
    Die unten aufgeführten Code-Ausschnitte sind u. U. gekürzt. Die tatsächlichen Implementierungen können umfangreicher sein!

### Datenbank

```sql
CREATE TABLE [dbo].[Abteilung](
	[AbteilungID] [int] IDENTITY(1,1) NOT NULL,
	[Standortkuerzel] [nvarchar](5) NOT NULL,
	[Abteilungskuerzel] [nvarchar](10) NOT NULL,
	[Abteilungsname] [nvarchar](50) NOT NULL,
	[Kostenstelle] [nvarchar](10) NULL,
	[Uebergeordnet] [int] NULL,
	[ErstelltAm] [datetime2](7) NOT NULL,
	[ErstelltVon] [nvarchar](75) NOT NULL,
	[GeaendertAm] [datetime2](7) NULL,
	[GeaendertVon] [nvarchar](75) NULL,
	[RecordID] [uniqueidentifier] NOT NULL,
 CONSTRAINT [PK_Abteilung] PRIMARY KEY CLUSTERED 
(
	[AbteilungID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
```

### Anwendung

```csharp
public class Department : ITrackable
{
	public const int DepartmentCodeMaxLength = 10;
	public const int DepartmentNameMaxLength = 50;
	public const int CostCenterMaxLength = 10;

	[...]

	public int DepartmentId
	{
		get;
		set;
	}

	public string LocationCode
	{
		get;
		set;
	}

	public Location Location
	{
		get;
		set;
	}

	public string DepartmentCode
	{
		get;
		set;
	}

	public string DepartmentName
	{
		get;
		set;
	}

	public string CostCenter
	{
		get;
		set;
	}

	public List<Employee> Employees
	{
		get;
		set;
	}

	public int? SuperordinateDepartmentId
	{
		get;
		set;
	}

	public Department SuperordinateDepartment
	{
		get;
		set;
	}

	public List<Department> SubordinateDepartments
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
		return DepartmentCode + " - " + DepartmentName + " (" + LocationCode + ")";
	}

	public override bool Equals(object obj)
	{
		if (obj == null)
		{
			return false;
		}

		Department otherDepartment = (obj as Department);

		if (otherDepartment == null)
		{
			return false;
		}

		if (this.DepartmentId.Equals(otherDepartment.DepartmentId))
		{
			return true;
		}
		else
		{
			return false;
		}
	}

	public override int GetHashCode()
	{
		return DepartmentId.GetHashCode();
	}
}
```

### Transfer

```js
{
        "departmentId": 3,
        "locationCode": "DEE1",
        "location": null,
        "departmentCode": "VWD",
        "departmentName": "Datenverarbeitung",
        "costCenter": "3660",
        "employees": [],
        "superordinateDepartmentId": 2,
        "superordinateDepartment": null,
        "subordinateDepartments": [],
        "createdAt": "2019-08-01T12:35:00Z",
        "createdBy": "roelofsph",
        "lastUpdatedAt": null,
        "lastUpdatedBy": null,
        "recordId": "a4540940-cbb7-4b2f-aa1f-b53f1d1f4ae1"
}
```