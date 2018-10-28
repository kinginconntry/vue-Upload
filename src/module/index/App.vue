<template>
    <div class="fileManagement">
        <div class="header clear">
            <div class="header-breadcrumb clear" >
                <span class="file-name" style="width:40px;">File</span>
            </div>
            <div class="header-upload">
                <span>Upload</span>
                <input type="file" id="file_input" class="uploadInput" multiple="multiple" @change="changeUploadFile(e)"/>
            </div>
        </div>
        <div class="information clear">
            <div class="information-allFileName">
                <input type="checkbox" class="allFileName-input" @change="selectAllInput" v-model="allSelect" value="0">
                <span class="allFileName-name">Name</span>
            </div>
            <span class="information-creator">Creator</span>
        </div>
        <!-- 主体 -->
        <ul class="fileList" @dragenter="dragenter($event)">
            <li class="fileList-item" v-for="(item,index) in fileArr" :key="index" @mouseover="mouseover(item)" @mouseout="mouseout(item)" :class="{isActive:item.isActive}" @click="enterFolder(item)">
                <input type="checkbox" name="fileCheckbox" class="item-checkout" v-model="fileInput" :value="item.value" @change="changeFileInput(item)" @click.stop="stopPropagation">
                <i class="file-img" :class="[{'editFile-img':item.file_Type==0},item.fileIcon]"></i>
                <div class="item-name">
                    <span class="file-name" v-show="item.fileNameEditHide">{{item.fileName}}
                        <div class="prompt">
                            <span class="prompt-name">{{item.completeFileName}}</span>
                            <span class="prompt-icon"></span>
                        </div>
                    </span>
                    <input type="text" class="edit-file-name" v-model="fileName" :placeholder='item.fileName' v-show="item.inputHide" @keyup.enter="EditFileNameSuccess(item)" autofocus="autofocus" @click.stop="stopPropagation" />
                </div>
                <div class="item-uploadManagement" v-show="item.hoverHide">
                    <span class="upload-name">{{item.uploadName}}</span>
                    <span class="upload-time">{{item.uploadTime}}</span>
                </div>
                <div class="operating clear" v-show="item.fileHide">
                    <div class="operating-item" @click.stop="dowloadFile()">
                        <i class="operating-icon"></i>
                        <div class="prompt">
                            <span class="prompt-name">下载文件夹</span>
                            <span class="prompt-icon"></span>
                        </div>
                    </div>
                    <!-- 移动 -->
                    <div class="operating-item" @click.stop="getFloderList()">
                        <i class="operating-icon mobile-file"></i>
                        <div class="prompt">
                            <span class="prompt-name">移动文件夹</span>
                            <span class="prompt-icon"></span>
                        </div>
                    </div>
                    <!-- 重命名 -->
                    <div class="operating-item" @click.stop="editFileName()">
                        <i class="operating-icon rename-file"></i>
                        <div class="prompt">
                            <span class="prompt-name">重命名文件</span>
                            <span class="prompt-icon"></span>
                        </div>
                    </div>
                    <!-- 更多 -->
                    <div class="operating-item" @click.stop="clickMore()">
                        <i class="operating-icon more-file"></i>
                        <div class="prompt">
                            <span class="prompt-name">更多操作</span>
                            <span class="prompt-icon"></span>
                        </div>
                    </div>
                </div>
            </li>
        </ul>
        <!--拖拽弹窗 -->
        <div class="dragFileView" @drop="dropEnd($event)" @dragover="dragover($event)" @dragleave="dragleave($event)" v-show="dropViewHide">
            <i class="dragFileView-img"></i>
            <span class="dragFileView-text">Drag and drop files to upload here</span>
        </div>
        <div>虚线内支持拖拽上传</div>
    </div>
</template>

<script>
    import '../../common/css/reset.min.css'
    export default {
        data() {
            return {
                docArr:['doc','docx'],
                xlsArr:['xls','xlsx'],
                pptArr:['ppt','pptx'],
                pngArr:['png','jpg','jpeg','gif','bmp','tiff','pcx','tga','exif','fpx','svg','psd','cdr','pcd','dxf','ufo','eps','ai','raw','WMF','webp'],
                pdfArr:['pdf'],
                movArr:['mp4','mov','avi','wmv','mpeg','mov','mkv','flv','f4v','m4v','rmvb','rm','3gp','dat','ts','mts','vob'],
                mp3Arr:['mp3'],
                zipArr:['zip'],
                topButton:'',
                fileListArr:[],
                systemCopyHide:true,
                copyMoveText:'',
                breadcrumbVal:'',
                breadcrumbOverflowIf:true,
                breadcrumbOverflowElse:false,
                systemHide:true,
                fileSuffix:'',
                newCreatFolderparentId:'',
                parentActiveBg:'',
                copyFirstArr:[],
                uploadfileHide:false,
                newCreaderForder: false,
                parentIndex: 0,
                creatFolderName: '',
                allSelect: [],
                delectHide: false,
                agencyRead: '',
                busiRead: '',
                ApermissionsArr: [],
                CpermissionsArr: [],
                permissionsHide: false,
                previewUrl: '',
                viewHide: false,
                candidateName: '',
                copyOrMoveTo: '',
                targetFolderId: 1,
                copyFileArr: [],
                copyForderlArr: [],
                Breadcrumb: [],
                elObj: [],
                sourceFolderIdArr: [],
                sourceFileIdArr: [],
                reName: 0,
                parentFolderId: 1,
                profileId: '',
                fileArr: [],
                listArr: [],
                operateHide: false,
                dropViewHide: false,
                fileName: '',
                moreClientX: 0,
                moreClientY: 0,
                moreMarginLeft: 0,
                flieLength: 0,
                arr: [],
                copyPopUpsHide: false,
                fileInput: [],
                informationIf: 1,
                fileTypeId: null,
 
            }
        },
        mounted() {
        },
        methods: {
            dowloadFile () {
              console.log('下载');
            },
            getFloderList () {
              console.log('移动');
            },
            editFileName () {
              console.log('重命名');
            },
            clickMore () {
              console.log('更多');
            },
            enterFolder (el) {
              console.log(el)
            },
            //移入
            mouseover(el) {
                el.hoverHide = false;
                el.isActive = true;
    
                if (el.file_Type == 0) {
                    el.systemFileHide = true;
                    el.fileHide = false;
                } else {
                    el.systemFileHide = false;
                    el.fileHide = true;
                }
            },
            //移出
            mouseout(el) {
                if (el.file_Type == 0) {
                    //系统文件
                    if (el.isHoverFlag == 1) {
                        el.isActive = true;
                        el.hoverHide = false;
                        el.systemFileHide = true;
                        el.fileHide = false;
                    } else {
                        el.isActive = false;
                        el.hoverHide = true;
                        el.systemFileHide = false;
                        el.fileHide = false;
                    }
                } else {
                    //非系统文件
                    if (el.isHoverFlag == 1) {
                        el.isActive = true;
                        el.hoverHide = false;
                        el.systemFileHide = false;
                        el.fileHide = true;
    
                    } else {
                        el.isActive = false;
                        el.hoverHide = true;
                        el.systemFileHide = false;
                        el.fileHide = false;
                    }
                }
            },
            //拖拽开始
            dragenter(event) {
                event.preventDefault();
                event.stopPropagation();
                this.dropViewHide = true;
            },
            //拖拽中
            dragover(event) {
                event.preventDefault();
                event.stopPropagation();
                this.dropViewHide = true;
            },
            //拖拽结束
            dropEnd(e) {
                e.preventDefault();
                e.stopPropagation();
                this.dropViewHide = false;
                this.fileList(e.dataTransfer);
            },
            //拖拽离开区域
            dragleave(e) {
                e.preventDefault();
                e.stopPropagation();
                this.dropViewHide = false;
            },
            //按钮上传文件
            changeUploadFile(e) {
                let files = document.getElementById('file_input').files;
                for(let i = 0;i<files.length;i++){
                    this.uploadFile(files[i]);
                }
            },
            //获取文件信息
            fileList(fileList) {

                let files = fileList.files;
                let that = this;
                var j = 0;
                let file_ = '';
                
                for (let i = 0; i < files.length; i++) {
                    if (fileList.items[i].kind) {
                        file_ = fileList.items[i].webkitGetAsEntry();
                    };
                    //判断是否为文件夹
                    if (file_.isFile) {
                        files[i].folder = false;
                        that.uploadFile(files[i]);
                    } else {
                        //文件夹处理 
                        //this.obj.folderList.push(folderObj);
                        this.folders(fileList.items[i]);
                    }
                };
            },
            //文件夹处理
            folders(files) {
                let _this = this;
                
                if (files.kind) {
                    files = files.webkitGetAsEntry();
                };

                files.createReader().readEntries(function(file) {
                    var interaterIndex = 0;
                    for (let i = 0; i < file.length; i++) {
    
                        if (file[i].isFile) {
                            if (file[i].name != '.DS_Store') {
                              _this.foldersAdd(file[i]);
                            }
                        } else {
                            _this.folders(file[i]);
                        }
                    }
                })
            },
            foldersAdd(entry, folderPath) {
                let that = this;
                
                entry.file(function(file) {
                  file.folder = true;
                  that.uploadFile(file);
                })
            },
            uploadFile(file) {
              let suffixName;
              let arr = file.name.split('.');
              arr.map((x,index)=>{
                suffixName = arr[arr.length - 1];
              });
              if(this.docArr.indexOf(suffixName)!=-1){      
                file.class = 'doc';
              }else if(this.xlsArr.indexOf(suffixName)!=-1){
                file.class = 'xls';
              }else if(this.pptArr.indexOf(suffixName)!=-1){
                file.class = 'ppt';
              }else if(this.pngArr.indexOf(suffixName)!=-1){
                file.class = 'png';
              }else if(this.zipArr.indexOf(suffixName)!=-1){
                file.class = 'zip';
              }else if(this.pdfArr.indexOf(suffixName)!=-1){
                file.class = 'pdf';
              }else if(this.movArr.indexOf(suffixName)!=-1){
                file.class = 'mov';
              }else if(this.mp3Arr.indexOf(suffixName)!=-1){
                file.class = 'mp3';
              }else{
                file.class = 'other';
              }
              

              this.fileArr.push({
                fileName: this.formatFont(file.name),
                fileNameEditHide:true,
                fileIcon: file.class,
                completeFileName:file.name,
                uploadTime:file.lastModifiedDate.toLocaleString().split(' ')[0],
                uploadName:'uploadName',
                hoverHide:true
              })
            },
            //格式化字体
            formatFont(el) {
                let start, end, name;
                if (el.length >= 15) {
                    start = el.slice(0, 15);
                    end = el.slice(el.length - 5, el.length);
                    return `${start}...${end}`
                } else {
                    return el
                }
            },
        }
    }
</script>

<style lang="less">
    @systemFileBg: url('../../common/img/icon-CandidateManagement-folder-system@2x.png') no-repeat;
    @publicFileBg: url('../../common/img/icon-CandidateManagement-folder-custom@2x.png') no-repeat;
    @isActiveFileBg: url('../../common/img/icon-CandidateManagement-folder-white@2x.png') no-repeat;
    .fileManagement {
        margin: 0 auto;
        width:800px;
        min-height: 1000px;
        height: 100%;
        background: #FFFFFF;
        position: relative;
        .header {
            padding: 35px 20px 21px;
            .file-name {
                float: left;
                max-width: 85px;
                height: 28px;
                line-height: 28px;
                font-size: 18px;
                color: #283644;
                font-weight: 700;
                overflow: hidden;
                text-overflow: ellipsis;
                white-space: nowrap;
            }
            .header-upload{
                float: right;
                width: 68px;
                height: 28px;
                background: #4C9DFF;
                border-radius: 2px;
                line-height: 28px;
                color: #fff;
                text-align: center;
                position: relative;
                cursor: pointer;
                .uploadInput{
                    position: absolute;
                    top:0;
                    left: 0;
                    width: 68px;
                    height: 28px;
                    opacity: 0;
                    cursor: pointer;
                }
            }
            .header-breadcrumb {
                width: 300px;
                float: left;
                overflow: hidden;
                text-overflow: ellipsis;
                white-space: nowrap;
                cursor: pointer;
            }
            .isActive {
                color: #979BA1;
            }
        }
        .information {
            padding: 0 20px 16px;
            border-bottom: 1px solid #E6ECF2;
            .information-allFileName {
                float: left;
                .allFileName-input {
                    width: 16px;
                    height: 16px;
                    margin: 0 14px 0 0;
                }
                .allFileName-name {
                    font-size: 14px;
                    color: #283644;
                    font-weight: 600;
                }
            }
            .information-creator {
                float: right;
                font-size: 14px;
                color: #283644;
                font-weight: 600;
            }
        }
        .fileList {
            min-height: 300px;
            border:1px dashed #3082e7;
            .fileList-item {
                width: 100%;
                height: 56px;
                padding: 0 20px;
                border-bottom: 1px solid #F2F5F8;
                .item-checkout {
                    float: left;
                    margin: 20px 0px 0 0px;
                    width: 16px;
                    height: 16px;
                    border: 1px solid #C0CCDA;
                    border-radius: 4px;
                }
                .item-name {
                    float: left;
                    .file-name {
                        width: 250px;
                        height: 56px;
                        line-height: 56px;
                        font-size: 14px;
                        color: #283644;
                        cursor: pointer;
                        display: block;
                        position: relative;
                        .prompt {
                            position: absolute;
                            bottom: 56px;
                            left: 100px;
                            width: 270px;
                            display: none;
                            .prompt-name {
                                padding: 14px 16px;
                                background: #334151;
                                box-shadow: 0 -1px 6px 0 rgba(0, 0, 0, 0.20);
                                border-radius: 3px;
                                font-size: 14px;
                                color: #FFFFFF;
                                display: block;
                                line-height: normal;
                                word-wrap: break-word;
                            }
                            .prompt-icon {
                                position: absolute;
                                left: 50%;
                                margin: 0 0 0 -3px;
                                width: 0;
                                height: 0;
                                width: 0;
                                height: 0;
                                border-top: 6px solid #334151;
                                border-left: 6px solid transparent;
                                border-right: 6px solid transparent;
                                border-bottom: 6px solid transparent
                            }
                        }
                    }
                    .edit-file-name {
                        width: 250px;
                        height: 30px;
                        margin: 13px 0 0 0;
                        border: 1px solid #E6ECF2;
                        padding-left: 6px;
                        font-size: 14px;
                    }
                    .file-name:hover .prompt {
                        display: block;
                    }
                }
                .item-uploadManagement {
                    float: right;
                    width: 150px;
                    text-align: right;
                    margin: 13px 0 0 0;
                    .upload-name,
                    .upload-time {
                        display: block;
                        font-size: 12px;
                        color: #979BA1;
                        overflow: hidden;
                        text-overflow: ellipsis;
                        white-space: nowrap;
                        font-weight: 100;
                    }
                }
                .operating {
                    float: right;
                    .operating-item {
                        width: 15px;
                        height: 15px;
                        float: left;
                        margin: 22px 0 0 16px;
                        position: relative;
                        cursor: pointer;
                        .operating-icon {
                            width: 14px;
                            height: 14px;
                            display: inline-block;
                            background: url('../../common/img/icon-CandidateManagement-file-download@2x.png') no-repeat;
                            background-size: contain;
                        }
                        .mobile-file {
                            background: url('../../common/img/icon-CandidateManagement-file-move@2x.png') no-repeat;
                            background-size: contain;
                        }
                        .rename-file {
                            background: url('../../common/img/icon-CandidateManagement-file-edit@2x.png') no-repeat;
                            background-size: contain;
                        }
                        .more-file {
                            background: url('../../common/img/icon-CandidateManagement-file-more@2x.png') no-repeat;
                            background-size: contain;
                        }
                        .prompt {
                            position: absolute;
                            bottom: 30px;
                            left: 0px;
                            width: 100px;
                            margin: 0 0 0 -46px;
                            display: none;
                            .prompt-name {
                                padding: 7px 8px;
                                background: #334151;
                                box-shadow: 0 -1px 6px 0 rgba(0, 0, 0, 0.20);
                                border-radius: 3px;
                                font-size: 12px;
                                color: #FFFFFF;
                                display: block;
                                text-align: center;
                                word-wrap: break-word;
                            }
                            .prompt-icon {
                                position: absolute;
                                left: 50%;
                                margin: 0 0 0 -3px;
                                width: 0;
                                height: 0;
                                width: 0;
                                height: 0;
                                border-top: 6px solid #334151;
                                border-left: 6px solid transparent;
                                border-right: 6px solid transparent;
                                border-bottom: 6px solid transparent
                            }
                        }
                        &:hover .prompt {
                            display: block;
                        }
                    }
                }
                .file-img {
                    float: left;
                    margin: 17px 12px 0 16px;
                    width: 24px;
                    height: 28px;
                    background: @publicFileBg;
                    background-size: contain;
                }
                .editFile-img {
                    float: left;
                    margin: 15px 12px 0 16px;
                    width: 24px;
                    height: 20px;
                    background: @systemFileBg;
                    background-size: contain;
                }
                .doc {
                    background: url('../../common/img/icon-CandidateManagement-file-doc@2x.png') no-repeat;
                    background-size: contain;
                }
                .xls {
                    background: url('../../common/img/icon-CandidateManagement-file-xls@2x.png') no-repeat;
                    background-size: contain;
                }
                .ppt {
                    background: url('../../common/img/icon-CandidateManagement-file-ppt@2x.png') no-repeat;
                    background-size: contain;
                }
                .pdf {
                    background: url('../../common/img/icon-CandidateManagement-file-pdf@2x.png') no-repeat;
                    background-size: contain;
                }
                .mov {
                    background: url('../../common/img/icon-CandidateManagement-file-mov@2x.png') no-repeat;
                    background-size: contain;
                }
                .mp3 {
                    background: url('../../common/img/icon-CandidateManagement-file-mp3@2x.png') no-repeat;
                    background-size: contain;
                }
                .zip {
                    background: url('../../common/img/icon-CandidateManagement-file-zip@2x.png') no-repeat;
                    background-size: contain;
                }
                .png {
                    background: url('../../common/img/icon-CandidateManagement-file-jpg@2x.png') no-repeat;
                    background-size: contain;
                }
                .other {
                    background: url('../../common/img/icon-CandidateManagement-file-other@2x.png') no-repeat;
                    background-size: contain;
                }
            }
            .isActive {
                background: #F9FAFC;
            }
        }
        .dragFileView {
            position: absolute;
            top: 0px;
            left: 0px;
            width: 800px;
            height: 465px;
            border: 1px dashed #4C9DFF;
            background: rgba(255, 255, 255, .9);
            z-index: 100;
            .dragFileView-img {
                display: block;
                width: 80px;
                height: 110px;
                margin: 80px auto 20px;
               // background: url('../../common/img/icon-backend-file-EmptyState@2x.png') no-repeat;
                background-size: contain;
            }
            .dragFileView-text {
                display: block;
                font-size: 14px;
                color: #5E6D82;
                width: 100%;
                text-align: center;
            }
        }
    }
</style>

