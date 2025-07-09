# constructing_accountability
This repo contains the scripts used to clean, analyse, and process data from the Mexican federal program Youth Building the Future. The scripts were used for the story ['El doble blindaje de Jóvenes Construyendo el Futuro'](https://www.meganoticias.mx/cdmx/noticia/el-doble-blindaje-de-jovenes-construyendo-el-futuro/589146) [spanish only] published by the Mexican outlet Meganoticias. 

## 🗂️Data Source & Original Structure
Data obtain from [Padrón Único de Beneficiarios del Programa Jóvenes COnstruyendo el Futuro](https://pub.bienestar.gob.mx/v2/pub/programasIntegrales/9/5928)

35 ```.xlsx``` files were downloaded:
- 2019-2020:  Annual files, each year split in two ```.xlsx``` files due to the amount of data

These files lacked information about the ```SEX, AGE, and REGISTRATION DATE ```

- 2021:       One file for the full year

- 2022-2023:  Monthly files 

Across all files the names of the beneficiaries were splited across columns ```Surname, Second Surname, Name``` and states were coded under ```CVE_ENTIDAD```

## 🧼Normalization & Standarization

### Annual files:
Manually addded the columns ```SEX, AGE, and REGISTRATION DATE ``` as empty values to match the structure of the later datasets.

### Monthly files:
Ensured consistency of the colum names across all files and were standarized:
```NUM, CVE_ENTIDAD, CVE_MUN, PRIMER APELLIDO, SEGUNDO APELLIDO, NOMBRE, SEXO, EDAD, FECHA_ALTA, IMPORTE_BENEFICIO```

---
### Name and State standarization

To clean and simplify structure the names were merged into a single ```NAME```column, which kept the full name in a single cell:
```python
def generate_full_name(row):
    return f"{row['NOMBRE']} {row['PRIMER APELLIDO']} {row['SEGUNDO APELLIDO']}"
df['NOMBRE'] = df.apply(generate_full_name, axis=1)

```
As for the states, instead of keeping the numeric codes, a dictionary was used to map each one to the corresponding name:

```python
state_mapping = {
    '01': 'Aguascalientes', '02': 'Baja California', '03': 'Baja California Sur', '04': 'Campeche',
    '05': 'Coahuila', '06': 'Colima', '07': 'Chiapas', '08': 'Chihuahua', '09': 'Ciudad de México',
    '10': 'Durango', '11': 'Guanajuato', '12': 'Guerrero', '13': 'Hidalgo', '14': 'Jalisco',
    '15': 'México', '16': 'Michoacán', '17': 'Morelos', '18': 'Nayarit', '19': 'Nuevo León',
    '20': 'Oaxaca', '21': 'Puebla', '22': 'Querétaro', '23': 'Quintana Roo', '24': 'San Luis Potosí',
    '25': 'Sinaloa', '26': 'Sonora', '27': 'Tabasco', '28': 'Tamaulipas', '29': 'Tlaxcala',
    '30': 'Veracruz', '31': 'Yucatán', '32': 'Zacatecas'
}
```

The final columns across all files were standarized as:
```NUM, CVE_ENTIDAD, CVE_MUN, PRIMER APELLIDO, SEGUNDO APELLIDO, NOMBRE, SEXO, EDAD, FECHA_ALTA, IMPORTE_BENEFICIO```

### UID Creation & De-duplication

To handle beneficiaries as unique individuals based on basic attributes, a simple concatenated string was used:

``python
UID = NOMBRE + "_" + CVE_ENTIDAD + "_" + CVE_MUNICIPIO
``

To make identifiers cleaner and consistent across dozens of large files, a ``hashed_UID`` was created using the SHA-256 algorithm (this version wasn't used given editorial decisions):
``python
def generate__hashed_uid(row):
    nombre = str(row.get('NOMBRE', '')).strip().upper()
    entidad = str(row.get('CVE_ENTIDAD', 'UNKNOWN')).zfill(2)
    municipio = str(row.get('CVE_MUNICIPIO', 'UNKNOWN')).zfill(3)
    uid_str = f"{nombre}_{entidad}_{municipio}"
    return hashlib.sha256(uid_str.encode()).hexdigest()
``

## 📜Scripts
1. [mapping_states](./https://github.com/TheIcebergTip/constructing_accountability/blob/main/Scripts/mapping_states)
2. [summarize_by_year](./https://github.com/TheIcebergTip/constructing_accountability/blob/main/Scripts/concat_uid_jcf)
3. [concat_uid_jcf](./https://github.com/TheIcebergTip/constructing_accountability/blob/main/Scripts/summarize_by_year)
4. [hashed_uid_jcf](./https://github.com/TheIcebergTip/constructing_accountability/blob/main/Scripts/hashed_uid_jcf)
5. [jcf_data_wrangler_deluxe](./https://github.com/TheIcebergTip/constructing_accountability/blob/main/Scripts/jcf_data_wrangler)

