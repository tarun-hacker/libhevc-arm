enable_language(C ASM)

cmake_minimum_required(VERSION 3.5)

set(HEVC_ROOT "${CMAKE_CURRENT_SOURCE_DIR}")
set(HEVC_CONFIG_DIR "${CMAKE_CURRENT_BINARY_DIR}")

if("${HEVC_ROOT}" STREQUAL "${HEVC_CONFIG_DIR}")
  message(
    FATAL_ERROR
      "Building from within the libhevc source tree is not supported.\n"
      "Hint: Run these commands\n"
      "$ rm -rf CMakeCache.txt CMakeFiles\n"
      "$ mkdir -p ./build\n"
      "$ cd ./build\n"
      "And re-run CMake from the build directory.")
endif()

set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
set(CMAKE_STATIC_LIBRARY_PREFIX "")

if(SANITIZE)
  string(TOLOWER ${SANITIZE} SANITIZE)

  set(CMAKE_SANITIZER_C_FLAGS "-fno-omit-frame-pointer -fsanitize=${SANITIZE}")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${CMAKE_SANITIZER_C_FLAGS}")
endif()

list(APPEND LIBHEVCDEC_SRCS
            "${HEVC_ROOT}/common/ithread.c"
            "${HEVC_ROOT}/decoder/ihevcd_api.c"
            "${HEVC_ROOT}/common/ihevc_quant_tables.c"
            "${HEVC_ROOT}/common/ihevc_inter_pred_filters.c"
            "${HEVC_ROOT}/common/ihevc_weighted_pred.c"
            "${HEVC_ROOT}/common/ihevc_padding.c"
            "${HEVC_ROOT}/common/ihevc_deblk_edge_filter.c"
            "${HEVC_ROOT}/common/ihevc_deblk_tables.c"
            "${HEVC_ROOT}/common/ihevc_cabac_tables.c"
            "${HEVC_ROOT}/common/ihevc_common_tables.c"
            "${HEVC_ROOT}/common/ihevc_intra_pred_filters.c"
            "${HEVC_ROOT}/common/ihevc_chroma_intra_pred_filters.c"
            "${HEVC_ROOT}/common/ihevc_mem_fns.c"
            "${HEVC_ROOT}/common/ihevc_sao.c"
            "${HEVC_ROOT}/common/ihevc_trans_tables.c"
            "${HEVC_ROOT}/common/ihevc_recon.c"
            "${HEVC_ROOT}/common/ihevc_itrans.c"
            "${HEVC_ROOT}/common/ihevc_itrans_recon.c"
            "${HEVC_ROOT}/common/ihevc_iquant_recon.c"
            "${HEVC_ROOT}/common/ihevc_iquant_itrans_recon.c"
            "${HEVC_ROOT}/common/ihevc_itrans_recon_32x32.c"
            "${HEVC_ROOT}/common/ihevc_itrans_recon_16x16.c"
            "${HEVC_ROOT}/common/ihevc_itrans_recon_8x8.c"
            "${HEVC_ROOT}/common/ihevc_chroma_itrans_recon.c"
            "${HEVC_ROOT}/common/ihevc_chroma_iquant_recon.c"
            "${HEVC_ROOT}/common/ihevc_chroma_iquant_itrans_recon.c"
            "${HEVC_ROOT}/common/ihevc_chroma_recon.c"
            "${HEVC_ROOT}/common/ihevc_chroma_itrans_recon_16x16.c"
            "${HEVC_ROOT}/common/ihevc_chroma_itrans_recon_8x8.c"
            "${HEVC_ROOT}/common/ihevc_buf_mgr.c"
            "${HEVC_ROOT}/common/ihevc_disp_mgr.c"
            "${HEVC_ROOT}/common/ihevc_dpb_mgr.c"
            "${HEVC_ROOT}/common/ithread.c"
            "${HEVC_ROOT}/decoder/ihevcd_version.c"
            "${HEVC_ROOT}/decoder/ihevcd_api.c"
            "${HEVC_ROOT}/decoder/ihevcd_decode.c"
            "${HEVC_ROOT}/decoder/ihevcd_nal.c"
            "${HEVC_ROOT}/decoder/ihevcd_bitstream.c"
            "${HEVC_ROOT}/decoder/ihevcd_parse_headers.c"
            "${HEVC_ROOT}/decoder/ihevcd_parse_slice_header.c"
            "${HEVC_ROOT}/decoder/ihevcd_parse_slice.c"
            "${HEVC_ROOT}/decoder/ihevcd_parse_residual.c"
            "${HEVC_ROOT}/decoder/ihevcd_cabac.c"
            "${HEVC_ROOT}/decoder/ihevcd_intra_pred_mode_prediction.c"
            "${HEVC_ROOT}/decoder/ihevcd_process_slice.c"
            "${HEVC_ROOT}/decoder/ihevcd_utils.c"
            "${HEVC_ROOT}/decoder/ihevcd_job_queue.c"
            "${HEVC_ROOT}/decoder/ihevcd_ref_list.c"
            "${HEVC_ROOT}/decoder/ihevcd_get_mv.c"
            "${HEVC_ROOT}/decoder/ihevcd_mv_pred.c"
            "${HEVC_ROOT}/decoder/ihevcd_mv_merge.c"
            "${HEVC_ROOT}/decoder/ihevcd_iquant_itrans_recon_ctb.c"
            "${HEVC_ROOT}/decoder/ihevcd_itrans_recon_dc.c"
            "${HEVC_ROOT}/decoder/ihevcd_common_tables.c"
            "${HEVC_ROOT}/decoder/ihevcd_boundary_strength.c"
            "${HEVC_ROOT}/decoder/ihevcd_deblk.c"
            "${HEVC_ROOT}/decoder/ihevcd_inter_pred.c"
            "${HEVC_ROOT}/decoder/ihevcd_sao.c"
            "${HEVC_ROOT}/decoder/ihevcd_ilf_padding.c"
            "${HEVC_ROOT}/decoder/ihevcd_fmt_conv.c")

list(
  APPEND LIBHEVCDEC_X86_SRCS
         "${HEVC_ROOT}/decoder/arm/ihevcd_fmt_conv_420sp_to_420p.s"
         "${HEVC_ROOT}/decoder/arm/ihevcd_fmt_conv_420sp_to_420sp.s"
         "${HEVC_ROOT}/decoder/arm/ihevcd_fmt_conv_420sp_to_rgba8888.s"
         "${HEVC_ROOT}/decoder/arm/ihevcd_function_selector_a9q.c"
         "${HEVC_ROOT}/decoder/arm/ihevcd_function_selector.c"
         "${HEVC_ROOT}/decoder/arm/ihevcd_function_selector_noneon.c"
         "${HEVC_ROOT}/decoder/arm/ihevcd_itrans_recon_dc_chroma.s"
         "${HEVC_ROOT}/decoder/arm/ihevcd_itrans_recon_dc_luma.s"
         "${HEVC_ROOT}/common/arm/ihevc_cmn_utils_neon.h"
         "${HEVC_ROOT}/common/arm/ihevc_deblk_chroma_horz.s"
         "${HEVC_ROOT}/common/arm/ihevc_deblk_chroma_vert.s"
         "${HEVC_ROOT}/common/arm/ihevc_deblk_luma_horz.s"
         "${HEVC_ROOT}/common/arm/ihevc_deblk_luma_vert.s"
         "${HEVC_ROOT}/common/arm/ihevc_func_selector.h"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_copy.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_copy_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_horz.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_horz_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_vert.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_vert_w16inp.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_vert_w16inp_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_chroma_vert_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_filters_luma_horz.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_filters_luma_vert.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_filters_luma_vert_w16inp.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_luma_copy.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_luma_copy_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_luma_horz_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_inter_pred_luma_vert_w16inp_w16out.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_dc.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_horz.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_mode_18_34.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_mode_27_to_33.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_mode2.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_mode_3_to_9.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_planar.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_chroma_ver.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_filters_chroma_mode_11_to_17.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_filters_chroma_mode_19_to_25.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_filters_luma_mode_11_to_17.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_filters_luma_mode_19_to_25.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_filters_neon_intr.c"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_dc.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_horz.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_mode_18_34.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_mode_27_to_33.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_mode2.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_mode_3_to_9.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_planar.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_pred_luma_vert.s"
         "${HEVC_ROOT}/common/arm/ihevc_intra_ref_substitution_a9q.c"
         "${HEVC_ROOT}/common/arm/ihevc_itrans_recon_16x16.s"
         "${HEVC_ROOT}/common/arm/ihevc_itrans_recon_32x32.s"
         "${HEVC_ROOT}/common/arm/ihevc_itrans_recon_4x4.s"
         "${HEVC_ROOT}/common/arm/ihevc_itrans_recon_4x4_ttype1.s"
         "${HEVC_ROOT}/common/arm/ihevc_itrans_recon_8x8.s"
         "${HEVC_ROOT}/common/arm/ihevc_mem_fns.s"
         "${HEVC_ROOT}/common/arm/ihevc_padding.s"
         "${HEVC_ROOT}/common/arm/ihevc_platform_macros.h"
         "${HEVC_ROOT}/common/arm/ihevc_quant_iquant_ssd_neon_intr.c"
         "${HEVC_ROOT}/common/arm/ihevc_resi_trans_32x32_a9q.s"
         "${HEVC_ROOT}/common/arm/ihevc_resi_trans_neon_32x32.c"
         "${HEVC_ROOT}/common/arm/ihevc_resi_trans_neon.c"
         "${HEVC_ROOT}/common/arm/ihevc_resi_trans.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_band_offset_chroma.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_band_offset_luma.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class0_chroma.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class0.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class1_chroma.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class1.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class2_chroma.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class2.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class3_chroma.s"
         "${HEVC_ROOT}/common/arm/ihevc_sao_edge_offset_class3.s"
         "${HEVC_ROOT}/common/arm/ihevc_weighted_pred_bi_default.s"
         "${HEVC_ROOT}/common/arm/ihevc_weighted_pred_bi.s"
         "${HEVC_ROOT}/common/arm/ihevc_weighted_pred_neon_intr.c"
         "${HEVC_ROOT}/common/arm/ihevc_weighted_pred_uni.s"
        )


set(LIBHEVCDEC_INCLUDES ${HEVC_ROOT}/common ${HEVC_ROOT}/decoder)

set(LIBHEVCDEC_X86_C_FLAGS "-DARM")
set(LIBHEVCDEC_X86_INCLUDES ${HEVC_ROOT}/common/arm ${HEVC_ROOT}/decoder/arm)
set(LIBHEVCDEC_C_FLAGS "${LIBHEVCDEC_X86_C_FLAGS}")

include_directories(${LIBHEVCDEC_INCLUDES} ${LIBHEVCDEC_X86_INCLUDES})
add_library(libhevcdec ${LIBHEVCDEC_SRCS} ${LIBHEVCDEC_X86_SRCS})
set_target_properties(libhevcdec
                      PROPERTIES COMPILE_FLAGS "${LIBHEVCDEC_C_FLAGS}")

list(APPEND HEVCDEC_SRCS "${HEVC_ROOT}/test/decoder/main.c")

add_executable(hevcdec ${HEVCDEC_SRCS} ${LIBHEVCDEC_X86_SRCS})
set_target_properties(hevcdec PROPERTIES COMPILE_FLAGS "-DMD5_DISABLE \
  -DPROFILE_ENABLE")
target_link_libraries(hevcdec libhevcdec Threads::Threads)
