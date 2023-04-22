#pragma warning (push)
#pragma warning (disable:number)


#include <gflags/gflags.h>
#include <cstdlib>
#include <exception>
#include <iostream>
#include <opencv2/highgui.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/opencv.hpp>
#include <thread>

#include <libyuv.h>
#include <vpl/mfx.h>


DEFINE_string(device, "/dev/video0", "Which device to run");
DEFINE_int32(width, 1280, "What image size(width) to stream from the device");
DEFINE_int32(height, 720, "What image size(height) to stream from the device");
DEFINE_bool(intel_gpu, true, "Decode MJPG to RGB with Intel GPU?");
DEFINE_bool(viewer, true, "Enable viewer");

constexpr uint8_t SOI_MARKER_1 = 0xFF;
constexpr uint8_t SOI_MARKER_2 = 0xD8;
constexpr uint32_t KEY_CODE_Q = 113;
constexpr uint32_t DECODER_WAIT_TIMEOUT = 60000;
#define BITSTREAM_BUFFER_SIZE 640 * 480 * 3 * 3  // 2000000

#define MAJOR_API_VERSION_REQUIRED 2
#define MINOR_API_VERSION_REQUIRED 2

#define VPLVERSION(major, minor) (major << 16 | minor)

uint16_t align16(uint16_t value) { return (((value + 15) >> 4) << 4); }

// Shows implementation info with oneVPL
void ShowImplementationInfo(mfxLoader loader, mfxU32 implnum) {
  mfxImplDescription *idesc = nullptr;
  mfxStatus sts;
  // Loads info about implementation at specified list location
  sts = MFXEnumImplementations(loader, implnum, MFX_IMPLCAPS_IMPLDESCSTRUCTURE, (mfxHDL *)&idesc);
  if (!idesc || (sts != MFX_ERR_NONE))
    return;

  printf("Implementation details:\n");
  printf("  ApiVersion:           %hu.%hu  \n", idesc->ApiVersion.Major, idesc->ApiVersion.Minor);
  printf("  Implementation type:  %s\n", (idesc->Impl == MFX_IMPL_TYPE_SOFTWARE) ? "SW" : "HW");
  printf("  AccelerationMode via: ");
  switch (idesc->AccelerationMode) {
    case MFX_ACCEL_MODE_NA:
      printf("NA \n");
      break;
    case MFX_ACCEL_MODE_VIA_D3D9:
      printf("D3D9\n");
      break;
    case MFX_ACCEL_MODE_VIA_D3D11:
      printf("D3D11\n");
      break;
    case MFX_ACCEL_MODE_VIA_VAAPI:
      printf("VAAPI\n");
      break;
    case MFX_ACCEL_MODE_VIA_VAAPI_DRM_MODESET:
      printf("VAAPI_DRM_MODESET\n");
      break;
    case MFX_ACCEL_MODE_VIA_VAAPI_GLX:
      printf("VAAPI_GLX\n");
      break;
    case MFX_ACCEL_MODE_VIA_VAAPI_X11:
      printf("VAAPI_X11\n");
      break;
    case MFX_ACCEL_MODE_VIA_VAAPI_WAYLAND:
      printf("VAAPI_WAYLAND\n");
      break;
    case MFX_ACCEL_MODE_VIA_HDDLUNITE:
      printf("HDDLUNITE\n");
      break;
    default:
      printf("unknown\n");
      break;
  }
  printf("  DeviceID:             %s \n", idesc->Dev.DeviceID);
  MFXDispReleaseImplDescription(loader, idesc);

  // Show implementation path, added in 2.4 API
  mfxHDL implPath = nullptr;
  sts = MFXEnumImplementations(loader, implnum, MFX_IMPLCAPS_IMPLPATH, &implPath);
  if (!implPath || (sts != MFX_ERR_NONE))
    return;

  printf("  Path: %s\n\n", reinterpret_cast<mfxChar *>(implPath));
  MFXDispReleaseImplDescription(loader, implPath);
}
