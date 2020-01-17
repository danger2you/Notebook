# 高压缩空间数据读取MeteoInfo
* Jar包名称: MeteoInfo
* 代码示例:

```java
package com.company;

import org.meteoinfo.data.GridData;
import org.meteoinfo.data.meteodata.DataInfo;
import org.meteoinfo.data.meteodata.MeteoDataInfo;
import org.meteoinfo.data.meteodata.Variable;
import org.meteoinfo.global.Extent;
import org.meteoinfo.script.MeteoInfoData;

import java.util.List;

/**
 * GRIB文件解析程序(支持GRIB1 AND GRIB2)
 *
 * @author chenwu@htdf.cc
 * @date 2018/7/19
 */
public class GRIBDemo {
    /**
     * grib文件路径
     */
    private final static String GRIB_FILE_URL = "C:\\Users\\chenw\\Desktop\\新建文件夹 (3)1\\W_NAFP_C_ECMF_P_C1D07181200072812001GRIB1.bin";
    /**
     * 读取的要素变量名称
     */
    private final static String VAR_NAME = "Temperature_isobaric";
    /**
     * 读取要素变量对应的层级/高度
     * 只有一个层级时，这里则直接给一个null即可
     */
    private final static Double HEIGHT = 850.0D;
    /**
     * 过滤格点
     */
    private final static Extent FILTER_EXTENT = new Extent(60D, 150D, -10D, 60D);

    /**
     * 主程序
     */
    private static void demo() {
        MeteoInfoData meteoInfoData = new MeteoInfoData();
        //读取文件
        MeteoDataInfo meteoDataInfo = meteoInfoData.openNetCDFData(GRIB_FILE_URL);
        //获取数据信息
        DataInfo dataInfo = meteoDataInfo.getDataInfo();
        //设置获取的变量名称
        meteoDataInfo.setVariableIndex(meteoDataInfo.getVariableIndex(VAR_NAME));
        //设置获取的变量对应的高度
        List<Double> levels = dataInfo.getVariable(VAR_NAME).getLevels();
        int leveIndex = levels != null && levels.contains(HEIGHT) ? levels.indexOf(HEIGHT) : 0;
        meteoDataInfo.setLevelIndex(leveIndex);
        //格点数据
        GridData gridData = FILTER_EXTENT == null ? meteoDataInfo.getGridData() : meteoDataInfo.getGridData().extract(FILTER_EXTENT);
        //测试输出
        //格点经度
        double[] xArray = gridData.xArray;
        //格点纬度
        double[] yArray = gridData.yArray;
        //格点数据
        double[][] data = gridData.data;
        //toStation
        for (int i = 0; i < xArray.length; i++) {
            for (int i1 = 0; i1 < yArray.length; i1++) {
                //经度、纬度、格点值
                System.out.println(xArray[i] + "," + yArray[i1] + "," + data[i1][i]);
            }
        }
    }

    public static void main(String[] args) {
        demo();
    }
}

```