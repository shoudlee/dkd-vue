<!-- 搜索区域 -->
<el-form :model="queryParams" ref="queryRef" :inline="true" v-show="showSearch" label-width="68px">
    <el-form-item label="点位名称" prop="nodeName">
        <el-input v-model="queryParams.nodeName" placeholder="请输入点位名称" clearable @keyup.enter="handleQuery" />
    </el-form-item>
    <el-form-item label="区域搜索" prop="regionId">
        <!-- <el-input v-model="queryParams.regionId" placeholder="请输入区域ID" clearable @keyup.enter="handleQuery" /> -->
        <el-select v-model="queryParams.regionId" placeholder="请选择区域" clearable>
            <el-option v-for="item in regionList" :key="item.id" :label="item.regionName" :value="item.id"></el-option>
        </el-select>
    </el-form-item>
    <el-form-item>
        <el-button type="primary" icon="Search" @click="handleQuery">搜索</el-button>
        <el-button icon="Refresh" @click="resetQuery">重置</el-button>
    </el-form-item>
</el-form>

<!-- 点位列表 -->
<el-table v-loading="loading" :data="nodeList" @selection-change="handleSelectionChange">
    <el-table-column type="selection" width="55" align="center" />
    <el-table-column label="序号" type="index" width="50" align="center" prop="id" />
    <el-table-column label="点位名称" align="center" prop="nodeName" />
    <el-table-column label="区域ID" align="center" prop="regionId" />
    <el-table-column label="商圈类型" align="center" prop="businessType">
        <template #default="scope">
<dict-tag :options="business_type" :value="scope.row.businessType" />
        </template>
    </el-table-column>
    <el-table-column label="合作商ID" align="center" prop="partnerId" />
    <el-table-column label="详细地址" align="center" prop="address" show-overflow-tooltip/>
    <el-table-column label="操作" align="center" class-name="small-padding fixed-width">
        <template #default="scope">
<el-button link type="primary" icon="Edit" @click="handleUpdate(scope.row)" v-hasPermi="['manage:node:edit']">修改</el-button>
<el-button link type="primary" icon="Delete" @click="handleDelete(scope.row)" v-hasPermi="['manage:node:remove']">删除</el-button>
        </template>
    </el-table-column>
</el-table>

<!-- 添加或修改点位管理对话框 -->
<el-dialog :title="title" v-model="open" width="500px" append-to-body>
  <el-form ref="nodeRef" :model="form" :rules="rules" label-width="100px">
    <el-form-item label="点位名称" prop="nodeName">
      <el-input v-model="form.nodeName" placeholder="请输入点位名称" />
    </el-form-item>
    <el-form-item label="所在区域" prop="regionId">
      <!-- <el-input v-model="form.regionId" placeholder="请输入区域ID" /> -->
        <el-select v-model="form.regionId" placeholder="请选择区域" clearable>
        <el-option v-for="item in regionList" :key="item.id" :label="item.regionName" :value="item.id"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="商圈类型" prop="businessType">
      <el-select v-model="form.businessType" placeholder="请选择商圈类型">
        <el-option v-for="dict in business_type" :key="dict.value" :label="dict.label"
          :value="parseInt(dict.value)"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="归属合作商" prop="partnerId">
      <!-- <el-input v-model="form.partnerId" placeholder="请输入合作商ID" /> -->
        <el-select v-model="form.partnerId" placeholder="请选择合作商" clearable>
        <el-option v-for="item in partnerList" :key="item.id" :label="item.partnerName" :value="item.id"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="详细地址" prop="address">
      <el-input type="textarea" v-model="form.address" placeholder="请输入详细地址" />
    </el-form-item>
  </el-form>
  <template #footer>
    <div class="dialog-footer">
      <el-button type="primary" @click="submitForm">确 定</el-button>
      <el-button @click="cancel">取 消</el-button>
    </div>
  </template>
</el-dialog>

<script setup name="Node">
    import { listPartner } from "@/api/manage/partner";
    import { listRegion } from "@/api/manage/region";
    import { loadAllParams } from "@/api/page";

    /* 查询所有的条件对象 */
    /* const loadAllParams = reactive({
      pageNum: 1,
      pageSize: 10000,
    }); */

    /* 查询合作商列表  */
    const partnerList = ref([]);
    function getPartnerList() {
        listPartner(loadAllParams).then(response => {
            partnerList.value = response.rows;
        });
    }
    getPartnerList();
    /* 查询区域列表 */
    const regionList = ref([]);
    function getRegionList() {
        listRegion(loadAllParams).then(response => {
            regionList.value = response.rows;
        });
    }
    getRegionList();    

</script>