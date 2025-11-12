# Persistencia de Datos con Room - PAMN

**Autor:** Javier Castilla  
**Asignatura:** Programación de Aplicaciones Móviles Nativas (PAMN)  
**Universidad:** Universidad de Las Palmas de Gran Canaria (ULPGC)  
**Codelab:** Persist data with Room  
**Fecha:** Noviembre 2025

---

## Descripción del Proyecto

Este proyecto implementa la persistencia de datos local en Android utilizando Room Database, la biblioteca de persistencia recomendada por Google. Se trata del codelab oficial "Persist data with Room" de Android Developers, que enseña a crear una aplicación de inventario con almacenamiento persistente de datos.

## Objetivos del Codelab

- Implementar Room Database para almacenamiento local
- Crear entidades, DAOs y la base de datos
- Aplicar el patrón Repository para la capa de datos
- Utilizar Kotlin Coroutines para operaciones asíncronas
- Implementar inyección de dependencias manual con Application Container

## Tecnologías Utilizadas

- **Lenguaje:** Kotlin 1.9.20
- **IDE:** Android Studio Hedgehog
- **SDK:** API 34 (Android 14)
- **Jetpack Compose:** UI moderna declarativa
- **Room Database:** 2.6.1
- **Kotlin Symbol Processing (KSP):** 1.9.20-1.0.14
- **Coroutines:** Para operaciones asíncronas
- **Navigation Compose:** Para navegación entre pantallas

## Estructura del Proyecto

- app/src/main/java/com/example/inventory/
    - data/
        - Item.kt
        - ItemDao.kt
        - InventoryDatabase.kt
        - ItemsRepository.kt
    - ui/
        - home/
            - HomeScreen.kt
        - item/
            - ItemEntryScreen.kt
        - navigation/
            - InventoryNavHost.kt
    - MainActivity.kt

## Arquitectura de Room

### 1. Entidad (Item.kt)

```
@Entity(tableName = "items")
data class Item(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val name: String,
    val price: Double,
    val quantity: Int
)
```

### 2. DAO (ItemDao.kt)

```
@Dao
interface ItemDao {
    @Insert(onConflict = OnConflictStrategy.IGNORE)
    suspend fun insert(item: Item)
    
    @Update
    suspend fun update(item: Item)
    
    @Delete
    suspend fun delete(item: Item)
}
```

### 3. Database (InventoryDatabase.kt)

```
@Database(entities = [Item::class], version = 1, exportSchema = false)
abstract class InventoryDatabase : RoomDatabase() {
    abstract fun itemDao(): ItemDao
}
```

## Conceptos Clave Aprendidos

### Persistencia de Datos Local

Room proporciona una capa de abstracción sobre SQLite que permite:

- Verificación de consultas SQL en tiempo de compilación
- Menor código boilerplate
- Integración fluida con LiveData y Flow
- Migración de esquemas simplificada

### Operaciones Asíncronas con Coroutines

Todas las operaciones de base de datos se ejecutan de forma asíncrona usando suspend functions:

```
viewModelScope.launch {
    itemsRepository.insertItem(item)
}
```

### Patrón Repository

El Repository actúa como fuente única de verdad y abstrae el origen de los datos:

```
class OfflineItemsRepository(private val itemDao: ItemDao) : ItemsRepository {
    override suspend fun insertItem(item: Item) = itemDao.insert(item)
}
```

### Inyección de Dependencias Manual

```
class InventoryApplication : Application() {
    lateinit var container: AppContainer
    override fun onCreate() {
        super.onCreate()
        container = AppDataContainer(this)
    }
}
```

## Aspectos Destacados

### 1. Verificación en Tiempo de Compilación

Room verifica todas las consultas SQL durante la compilación, detectando errores antes de ejecutar la app.

### 2. KSP vs KAPT

Este proyecto utiliza KSP (Kotlin Symbol Processing) en lugar de KAPT, proporcionando:

- Compilación hasta 2x más rápida
- Menor uso de memoria
- Mejor integración con Kotlin

### 3. Database Inspector

Android Studio incluye el Database Inspector que permite:

- Ver datos en tiempo real mientras la app se ejecuta
- Ejecutar consultas SQL personalizadas
- Modificar datos directamente para pruebas

Para acceder: `View > Tool Windows > App Inspection > Database Inspector`

## Funcionalidades Implementadas

- Crear base de datos Room persistente
- Insertar items en la base de datos
- Definir entidades con anotaciones
- Crear DAOs con operaciones CRUD
- Implementar Repository pattern
- Usar coroutines para operaciones asíncronas
- Configurar inyección de dependencias manual

## Verificación de Persistencia

Los datos se guardan en: `/data/data/com.example.inventory/databases/inventory_database`

Para verificar que los datos persisten correctamente:

1. Ejecutar la app en el emulador
2. Añadir varios items desde la UI
3. Abrir Database Inspector en Android Studio
4. Verificar que los items aparecen en la tabla items
5. Cerrar y reiniciar la app
6. Los datos deben permanecer almacenados

## Configuración de Gradle

Dependencias clave en `build.gradle.kts`:

```
implementation("androidx.room:room-runtime:2.6.1")
ksp("androidx.room:room-compiler:2.6.1")
implementation("androidx.room:room-ktx:2.6.1")

id("com.google.devtools.ksp") version "1.9.20-1.0.14"
```

## Recursos Adicionales

- [Documentación oficial de Room](https://developer.android.com/training/data-storage/room)
- [Codelab: Persist data with Room](https://developer.android.com/codelabs/basic-android-kotlin-compose-persisting-data-room)
- [Kotlin Coroutines Guide](https://kotlinlang.org/docs/coroutines-guide.html)

## Conclusiones

Este codelab me ha permitido comprender la importancia de la persistencia de datos en aplicaciones móviles y cómo Room simplifica significativamente el trabajo con bases de datos SQLite en Android. La integración con Coroutines y el patrón Repository proporciona una arquitectura limpia y mantenible para la gestión de datos.

---

**Universidad de Las Palmas de Gran Canaria**  
*Escuela de Ingeniería Informática*  
*Grado en Ingeniería Informática*

