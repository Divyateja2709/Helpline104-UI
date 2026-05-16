# Helpline104-UI Migration Guide
## Angular 4.1.3 → Angular 19 + Zard UI + Tailwind CSS v4

## Current Stack (Legacy)
| Item | Version |
|---|---|
| Angular | 4.1.3 |
| UI Library | Angular Material 2.0.0-beta.11 |
| HTTP | @angular/http (deprecated) |
| RxJS | 5.x (prototype patching) |
| CSS | Bootstrap 3 |
| AppModule | Monolithic (530 lines, 118 declarations) |

## Target Stack (Modern)
| Item | Version |
|---|---|
| Angular | 19 |
| UI Library | Zard UI (shadcn for Angular) |
| HTTP | @angular/common/http (functional interceptors) |
| RxJS | 7.x (pipe operators) |
| CSS | Tailwind CSS v4 |
| Architecture | Standalone components, lazy-loaded routes |

---

## Dependencies to Remove
- `@angular/http`
- `@angular/material` (beta)
- `bootstrap` + `jquery` + `tether`
- `angular2-toaster`
- `ng2-smart-table`
- `md2`
- `jspdf` (replace with modern alternative)

## Dependencies to Upgrade
| Old | New |
|---|---|
| `socket.io-client` 2.x | 4.x |
| `d3` 4.x | 7.x |
| `xlsx` 0.13 | 0.20 |
| `moment` | `date-fns` |

---

## Migration Approach
Fresh Angular 19 scaffold + module-by-module port.
Incremental upgrade (4→5→...→19) is NOT recommended
since every file requires changes regardless.

### Scaffold Command
```bash
ng new helpline104-ui --style=scss --routing=true --standalone
npx @ngzard/ui init
```

---

## Port Order (Module by Module)

### Phase 1 — Core Infrastructure (Week 1-2)
- [ ] Angular 19 scaffold with Zard UI + Tailwind CSS v4
- [ ] HTTP layer: `@angular/http` → `@angular/common/http`
- [ ] Auth guards: class-based `CanActivate` → functional `CanActivateFn`
- [ ] Session storage service
- [ ] Config service
- [ ] All 67 services: RxJS 5 → 7 pipe operators

### Phase 2 — Auth Flow (Week 3-4) [Midpoint Milestone]
- [ ] Login component
- [ ] Captcha component
- [ ] Role selection component
- [ ] Dashboard component
- [ ] Shared components: 20 directives, 3 pipes, common dialog, loader

### Phase 3 — Feature Modules (Week 5-8)
- [ ] Beneficiary registration module (2,282 lines → decompose)
- [ ] Call management module
- [ ] Case sheet module (2,900 lines → decompose)
- [ ] SIO services module
- [ ] Supervisor module
- [ ] Role-specific modules

### Phase 4 — Integrations + Polish (Week 9-12)
- [ ] Socket.io upgrade to 4.x with Observable patterns
- [ ] Replace all Angular Material beta → Zard UI equivalents
- [ ] Replace Bootstrap 3 → Tailwind CSS v4
- [ ] WAR packaging via pom.xml verified
- [ ] Production build clean: `ng build --configuration=production`

---

## Key Patterns Reference

### HTTP Migration
```typescript
// OLD (Angular 4)
import { Http } from '@angular/http';
this.http.get(url).map(res => res.json())

// NEW (Angular 19)
import { HttpClient } from '@angular/common/http';
this.http.get<ResponseType>(url)
```

### RxJS Migration
```typescript
// OLD (RxJS 5)
import 'rxjs/add/operator/map';
Observable.throw(error)

// NEW (RxJS 7)
import { map } from 'rxjs/operators';
import { throwError } from 'rxjs';
throwError(() => new Error(error))
```

### Auth Guard Migration
```typescript
// OLD (Angular 4)
export class AuthGuard implements CanActivate {
  canActivate(): boolean { ... }
}

// NEW (Angular 19)
export const authGuard: CanActivateFn = (route, state) => { ... }
```

### CSS Migration
```html
<!-- OLD (Bootstrap 3) -->
<div class="col-xs-12 col-md-6">

<!-- NEW (Tailwind v4) -->
<div class="w-full md:w-1/2">
```

---

## Risk Mitigation
- Run old and new apps against the same backend simultaneously
- Port module by module, verify each against existing backend before moving on
- Use AI coding tools (Claude Code) for mechanical conversions,
  manually review all output for business logic correctness
