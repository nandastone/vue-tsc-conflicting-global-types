Monorepo setup with `package-a` and `package-b` being built in library mode, and emitting types.

`package-b` is then importing `package-a`.

```bash
pnpm i`
cd packages/package-a
pnpm build
cd ../package-b
pnpm build
```

Result:

```bash
> pnpm build

> package-b@1.0.0 build /Test/vite-vue-monorepo/packages/package-b
> vite build && pnpm build-types

vite v5.2.8 building for production...
✓ 4 modules transformed.
dist/package-a.js  0.63 kB │ gzip: 0.31 kB
✓ built in 81ms

> package-b@1.0.0 build-types /Test/vite-vue-monorepo/packages/package-b
> vue-tsc --project tsconfig.build-types.json --emitDeclarationOnly

src/ComponentB.vue:2:1 - error TS6200: Definitions of the following identifiers conflict with those in another file: __VLS_IntrinsicElements, __VLS_Element, __VLS_GlobalComponents, __VLS_IsAny, __VLS_PickNotAny, __VLS_intrinsicElements, __VLS_SelfComponent, __VLS_WithComponent, __VLS_FillingEventArg_ParametersLength, __VLS_FillingEventArg, __VLS_FunctionalComponentProps, __VLS_AsFunctionOrAny, __VLS_UnionToIntersection, __VLS_OverloadUnionInner, __VLS_OverloadUnion, __VLS_ConstructorOverloads, __VLS_NormalizeEmits, __VLS_PrettifyGlobal

2 import { ComponentA } from "package-a";
  ~~~~~~

  ../package-a/dist/types/ComponentA.vue.d.ts:1:1
    1 declare const _default: import("vue").DefineComponent<__VLS_TypePropsToOption<{
      ~~~~~~~
    Conflicts are in this file.


Found 1 error in src/ComponentB.vue:2

 ELIFECYCLE  Command failed with exit code 2.
 ELIFECYCLE  Command failed with exit code 1.
```

Review [packages/package-a/dist/types/ComponentA.vue.d.ts](packages/package-a/dist/types/ComponentA.vue.d.ts) to see:

```typescript
declare global {
  type __VLS_IntrinsicElements = __VLS_PickNotAny<
    import("vue/jsx-runtime").JSX.IntrinsicElements,
    __VLS_PickNotAny<globalThis.JSX.IntrinsicElements, Record<string, any>>
  >;
  // ... etc
}
```

Trying to build `package-b` is attempting to emit `.d.ts` with the same global type definitions, when they already exist from `package-a`.
