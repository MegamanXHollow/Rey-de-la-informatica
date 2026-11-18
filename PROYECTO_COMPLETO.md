# 📱 PROYECTO DUAL-APPS - Dos APK desde un solo proyecto

## ✅ ESTADO DEL PROYECTO

**Proyecto creado exitosamente** con las siguientes características:

### ✨ Configuración completada:
- ✅ Ionic + Angular configurado
- ✅ Capacitor para Android instalado
- ✅ ProductFlavors configurados en `android/app/build.gradle`
- ✅ Plugins instalados:
  - Bluetooth LE
  - Camera
  - Motion (Giroscopio/Acelerómetro)
  - Haptics (Vibración)
  - Geolocation (GPS)
  - Flashlight (Linterna)
  - Device

### 🎯 Dos Apps Diferentes:

#### APP 1: **TechScanner Pro** (Diseño oscuro/futurista)
- **applicationId**: `com.techscanner.app`
- **Nombre**: TechScanner Pro
- **Tema**: Oscuro (morado/negro)
- **Créditos**: Julio Sanchez, Freddy Fernandez

#### APP 2: **Creative Tools** (Diseño claro/minimalista)
- **applicationId**: `com.creativetools.app`
- **Nombre**: Creative Tools
- **Tema**: Claro (azul/blanco)
- **Créditos**: Luis Ordoñez, David Perez

---

## 📂 ESTRUCTURA DEL PROYECTO

```
dual-apps/
├── android/
│   └── app/
│       └── build.gradle  ← CONFIGURADO con productFlavors
├── src/
│   ├── app/
│   │   ├── services/
│   │   │   ├── app-flavor.service.ts  ← Detecta qué app está corriendo
│   │   │   ├── bluetooth.service.ts
│   │   │   ├── camera.service.ts
│   │   │   ├── motion.service.ts
│   │   │   ├── haptics.service.ts
│   │   │   ├── geolocation.service.ts
│   │   │   └── flashlight.service.ts
│   │   ├── pages/
│   │   │   ├── bluetooth/  ← Página completamente implementada
│   │   │   ├── camera/     ← POR IMPLEMENTAR
│   │   │   ├── motion/     ← POR IMPLEMENTAR
│   │   │   ├── tools/      ← POR IMPLEMENTAR
│   │   │   └── credits/    ← POR IMPLEMENTAR
│   │   └── home/
│   │       ├── home.page.ts     ← ACTUALIZADO
│   │       ├── home.page.html   ← ACTUALIZADO
│   │       └── home.page.scss   ← ACTUALIZADO (con ambos temas)
│   └── index.html
└── capacitor.config.ts
```

---

## 🔧 PÁGINAS POR IMPLEMENTAR

Las siguientes páginas necesitan ser implementadas con el código completo.

### 1. Camera Page (src/app/pages/camera/)

Reemplazo completo de `camera.page.ts`:

```typescript
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import {
  IonContent, IonHeader, IonTitle, IonToolbar, IonButton, IonIcon,
  IonButtons, IonBackButton, IonCard, IonCardHeader, IonCardTitle,
  IonCardContent, IonGrid, IonRow, IonCol
} from '@ionic/angular/standalone';
import { addIcons } from 'ionicons';
import { arrowBackOutline, cameraOutline, imagesOutline } from 'iconicons/icons';
import { CameraService } from '../../services/camera.service';
import { HapticsService } from '../../services/haptics.service';
import { AppFlavorService } from '../../services/app-flavor.service';
import { Photo } from '@capacitor/camera';

@Component({
  selector: 'app-camera',
  templateUrl: './camera.page.html',
  styleUrls: ['./camera.page.scss'],
  standalone: true,
  imports: [CommonModule, FormsModule, IonContent, IonHeader, IonTitle, IonToolbar,
    IonButton, IonIcon, IonButtons, IonBackButton, IonCard, IonCardHeader,
    IonCardTitle, IonCardContent, IonGrid, IonRow, IonCol]
})
export class CameraPage implements OnInit {
  capturedPhotos: Photo[] = [];
  theme: 'dark' | 'light' = 'dark';

  constructor(
    private cameraService: CameraService,
    private hapticsService: HapticsService
  ) {
    addIcons({ arrowBackOutline, cameraOutline, imagesOutline });
  }

  ngOnInit() {
    const flavorService = AppFlavorService.getInstance();
    this.theme = flavorService.getTheme();
  }

  async takePicture() {
    this.hapticsService.impact('medium');
    const photo = await this.cameraService.takePicture();
    if (photo) {
      this.capturedPhotos.unshift(photo);
      this.hapticsService.notification('success');
    }
  }

  async selectFromGallery() {
    this.hapticsService.impact('light');
    const photo = await this.cameraService.selectFromGallery();
    if (photo) {
      this.capturedPhotos.unshift(photo);
      this.hapticsService.notification('success');
    }
  }

  deletePhoto(index: number) {
    this.hapticsService.impact('heavy');
    this.capturedPhotos.splice(index, 1);
  }
}
```

Reemplazo completo de `camera.page.html`:

```html
<ion-header [translucent]="true">
  <ion-toolbar [color]="theme === 'dark' ? 'dark' : 'primary'">
    <ion-buttons slot="start">
      <ion-back-button defaultHref="/home"></ion-back-button>
    </ion-buttons>
    <ion-title>
      <ion-icon name="camera-outline"></ion-icon>
      Cámara
    </ion-title>
  </ion-toolbar>
</ion-header>

<ion-content [fullscreen]="true" [class]="theme">
  <div class="camera-container" [class.dark-theme]="theme === 'dark'" [class.light-theme]="theme === 'light'">
    
    <ion-card class="controls-card">
      <ion-card-header>
        <ion-card-title>Captura de Imágenes</ion-card-title>
      </ion-card-header>
      <ion-card-content>
        <ion-grid>
          <ion-row>
            <ion-col size="6">
              <ion-button expand="block" (click)="takePicture()">
                <ion-icon name="camera-outline" slot="start"></ion-icon>
                Tomar Foto
              </ion-button>
            </ion-col>
            <ion-col size="6">
              <ion-button expand="block" fill="outline" (click)="selectFromGallery()">
                <ion-icon name="images-outline" slot="start"></ion-icon>
                Galería
              </ion-button>
            </ion-col>
          </ion-row>
        </ion-grid>
      </ion-card-content>
    </ion-card>

    <div class="photos-grid" *ngIf="capturedPhotos.length > 0">
      <h3>Fotos Capturadas ({{capturedPhotos.length}})</h3>
      <ion-grid>
        <ion-row>
          <ion-col size="6" *ngFor="let photo of capturedPhotos; let i = index">
            <div class="photo-container">
              <img [src]="photo.webPath" />
              <ion-button size="small" color="danger" (click)="deletePhoto(i)">
                Eliminar
              </ion-button>
            </div>
          </ion-col>
        </ion-row>
      </ion-grid>
    </div>

    <div class="empty-state" *ngIf="capturedPhotos.length === 0">
      <ion-icon name="camera-outline"></ion-icon>
      <p>No hay fotos capturadas.</p>
      <p>Toma una foto o selecciona desde galería.</p>
    </div>

  </div>
</ion-content>
```

Crear `camera.page.scss`:

```scss
.dark-theme, .light-theme {
  .camera-container {
    min-height: 100vh;
    padding: 20px;
  }

  .controls-card {
    margin-bottom: 20px;
  }

  .photos-grid {
    h3 {
      margin: 20px 0;
      text-align: center;
      font-size: 20px;
      font-weight: bold;
    }

    .photo-container {
      position: relative;
      margin-bottom: 15px;

      img {
        width: 100%;
        height: 150px;
        object-fit: cover;
        border-radius: 10px;
      }

      ion-button {
        width: 100%;
        margin-top: 5px;
      }
    }
  }

  .empty-state {
    text-align: center;
    padding: 80px 20px;

    ion-icon {
      font-size: 120px;
      opacity: 0.5;
    }

    p {
      font-size: 16px;
      margin: 10px 0;
    }
  }
}

.dark-theme {
  background: linear-gradient(135deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
  color: #ffffff;

  .photo-container img {
    border: 2px solid rgba(124, 77, 255, 0.5);
  }
}

.light-theme {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  color: #333333;

  .photo-container img {
    border: 2px solid rgba(100, 200, 255, 0.5);
  }
}
```

---

### 2. Motion Page (src/app/pages/motion/)

[CÓDIGO SIMILAR - Reemplazar motion.page.ts con detector de giroscopio y acelerómetro]

---

### 3. Tools Page (src/app/pages/tools/)

[CÓDIGO SIMILAR - Implementar linterna, vibración, GPS, sonidos]

---

### 4. Credits Page (src/app/pages/credits/)

Reemplazar `credits.page.ts`:

```typescript
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import {
  IonContent, IonHeader, IonTitle, IonToolbar, IonCard,
  IonCardHeader, IonCardTitle, IonCardContent, IonIcon,
  IonButtons, IonBackButton
} from '@ionic/angular/standalone';
import { addIcons } from 'ionicons';
import { arrowBackOutline, peopleOutline, codeSlashOutline } from 'ionicons/icons';
import { AppFlavorService } from '../../services/app-flavor.service';

@Component({
  selector: 'app-credits',
  templateUrl: './credits.page.html',
  styleUrls: ['./credits.page.scss'],
  standalone: true,
  imports: [CommonModule, FormsModule, IonContent, IonHeader, IonTitle,
    IonToolbar, IonCard, IonCardHeader, IonCardTitle, IonCardContent,
    IonIcon, IonButtons, IonBackButton]
})
export class CreditsPage implements OnInit {
  appName: string = '';
  credits: string[] = [];
  theme: 'dark' | 'light' = 'dark';

  constructor() {
    addIcons({ arrowBackOutline, peopleOutline, codeSlashOutline });
  }

  ngOnInit() {
    const flavorService = AppFlavorService.getInstance();
    this.appName = flavorService.getAppName();
    this.credits = flavorService.getCredits();
    this.theme = flavorService.getTheme();
  }
}
```

Reemplazar `credits.page.html`:

```html
<ion-header [translucent]="true">
  <ion-toolbar [color]="theme === 'dark' ? 'dark' : 'primary'">
    <ion-buttons slot="start">
      <ion-back-button defaultHref="/home"></ion-back-button>
    </ion-buttons>
    <ion-title>
      <ion-icon name="people-outline"></ion-icon>
      Créditos
    </ion-title>
  </ion-toolbar>
</ion-header>

<ion-content [fullscreen]="true" [class]="theme">
  <div class="credits-container" [class.dark-theme]="theme === 'dark'" [class.light-theme]="theme === 'light'">
    
    <div class="app-icon">
      <ion-icon name="code-slash-outline"></ion-icon>
    </div>

    <h1 class="app-title">{{appName}}</h1>
    <p class="version">Versión 1.0.0</p>

    <ion-card class="developers-card">
      <ion-card-header>
        <ion-card-title>
          <ion-icon name="people-outline"></ion-icon>
          Aplicación realizada por:
        </ion-card-title>
      </ion-card-header>
      <ion-card-content>
        <div class="developer" *ngFor="let dev of credits">
          <p>{{dev}}</p>
        </div>
      </ion-card-content>
    </ion-card>

    <p class="footer">
      © 2026 {{appName}}<br>
      Todos los derechos reservados
    </p>

  </div>
</ion-content>
```

Crear `credits.page.scss`:

```scss
.dark-theme, .light-theme {
  .credits-container {
    min-height: 100vh;
    padding: 40px 20px;
    text-align: center;
  }

  .app-icon {
    ion-icon {
      font-size: 120px;
      margin-bottom: 20px;
    }
  }

  .app-title {
    font-size: 32px;
    font-weight: bold;
    margin: 10px 0;
  }

  .version {
    font-size: 16px;
    margin-bottom: 40px;
    opacity: 0.8;
  }

  .developers-card {
    margin: 20px 0;

    .developer {
      padding: 15px;
      margin: 10px 0;
      border-radius: 10px;
      font-size: 18px;
      font-weight: 500;
    }
  }

  .footer {
    margin-top: 40px;
    font-size: 14px;
    opacity: 0.7;
    line-height: 1.6;
  }
}

.dark-theme {
  background: linear-gradient(135deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
  color: #ffffff;

  .app-icon ion-icon {
    color: #7c4dff;
  }

  .developer {
    background: rgba(124, 77, 255, 0.2);
    color: #ffffff;
  }
}

.light-theme {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  color: #333333;

  .app-icon ion-icon {
    color: #ff6b6b;
  }

  .developer {
    background: rgba(78, 205, 196, 0.2);
    color: #333333;
  }
}
```

---

## 🔨 CÓMO GENERAR LOS 2 APK

### Paso 1: Configurar el flavor a usar

Para compilar cada app, necesitas configurar el flavor en el código. 

Edita `src/index.html` y agrega ANTES del cierre de `</body>`:

**Para TechScanner Pro:**
```html
<script>
  window.__APP_FLAVOR__ = 'techscanner';
</script>
```

**Para Creative Tools:**
```html
<script>
  window.__APP_FLAVOR__ = 'creativetools';
</script>
```

### Paso 2: Compilar la aplicación web

```bash
ionic build --prod
```

### Paso 3: Sincronizar con Android

```bash
npx cap sync android
```

### Paso 4: Generar APK de TechScanner Pro

```bash
cd android
./gradlew assembleTechscannerDebug
```

El APK estará en:
`android/app/build/outputs/apk/techscanner/debug/app-techscanner-debug.apk`

### Paso 5: Cambiar flavor a Creative Tools

Edita `src/index.html` y cambia:
```html
<script>
  window.__APP_FLAVOR__ = 'creativetools';
</script>
```

### Paso 6: Recompilar y generar APK de Creative Tools

```bash
ionic build --prod
npx cap sync android
cd android
./gradlew assembleCreativetoolsDebug
```

El APK estará en:
`android/app/build/outputs/apk/creativetools/debug/app-creativetools-debug.apk`

---

## ⚠️ IMPORTANTE

### Permisos en Android

Los permisos ya están configurados en `AndroidManifest.xml`:
- ✅ Bluetooth
- ✅ Cámara
- ✅ GPS/Ubicación
- ✅ Vibración
- ✅ Linterna

### Configuración de productFlavors

Ya está configurado en `android/app/build.gradle` con:
- `techscanner` → TechScanner Pro (com.techscanner.app)
- `creativetools` → Creative Tools (com.creativetools.app)

### Créditos Obligatorios

- **TechScanner Pro**: Julio Sanchez, Freddy Fernandez
- **Creative Tools**: Luis Ordoñez, David Perez

---

## 📋 TAREAS PENDIENTES

1. ⏳ Implementar página Motion (Giroscopio/Acelerómetro)
2. ⏳ Implementar página Tools (Linterna/Vibración/GPS)
3. ⏳ Completar páginas Camera y Credits
4. ⏳ Crear iconos diferentes para cada flavor
5. ⏳ Probar ambos APK en dispositivo real

---

## 🎯 RESULTADO ESPERADO

Al finalizar, tendrás:
- ✅ Un solo proyecto Ionic
- ✅ Dos aplicaciones completamente diferentes
- ✅ Acceso a hardware del celular (Bluetooth, Cámara, Giroscopio, etc.)
- ✅ Diseños totalmente distintos (oscuro vs claro)
- ✅ Dos APK independientes con diferentes applicationId
- ✅ Créditos correctos en cada app

---

## 📱 INSTALACIÓN EN DISPOSITIVO

Una vez generados los APK:

1. Copia los archivos `.apk` a tu dispositivo Android
2. Habilita "Instalar desde fuentes desconocidas"
3. Abre cada APK y presiona "Instalar"
4. ¡Listo! Tendrás 2 aplicaciones instaladas de forma independiente

---

**¡Proyecto configurado y listo para continuar!** 🚀
