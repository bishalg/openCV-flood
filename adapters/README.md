# Hardware & Technology Adapters

Concrete implementations of the core abstraction interfaces, plus the stable
foreign-function boundary. Everything here is swappable; the core depends only
on `core/include/curv/interfaces/`.

## Implemented (public flood demo)

- **`capi/`**: stable C-ABI shared library (`libcurv_capi`).
- **`opencv/`**: anti-aliased sub-pixel overlay renderer + SVG export.
- **`cctv/`**: frame source, ring buffer, recorded video (`CctvVideoSource`),
  HTTP snapshot pull (`CctvHttpSnapshot`) for Caltrans / mock demos.
- **`android/`** / **`apple/`**: JNI + Swift wrappers over the C-ABI (bindings
  source only in this public demo).

## Milestone status

- **Done**: C-ABI, overlays, CCTV static → video → live snapshot path (M9–M11).
- **Next**: Phase 5 flood-alert production demo on the Cesium twin (M13).
