<script setup>
import { computed, ref } from 'vue';
import { Notifications } from '../axios/Notifications';

const {
  notifications,
  unreadCount,
  showNotifications,
  showDeleteNotificationModal,
  deleteIndex,
  toggleNotifications,
  deleteNotification,
  deleteAllNotifications,
  changeReadStatus,
  formatTime,
  handleClickOutside,
  openDeleteModal,
  closeDeleteModal
} = Notifications();

var notificationType = ref('ALL');

  // 필터링된 알림
  const filterNotifications = computed(() => {
    if(notificationType.value === 'ALL') {
      return notifications.value;
    } 
    else {
      return notifications.value.filter(n => n.notiType === notificationType.value);
    }
   });

   //카테고리 배열 생성
  const notiCatregorys = computed(() => {
    const categories = []; // 새로운 배열 생성
    categories.push('ALL'); // 전체 카테고리 추가
    notifications.value.forEach((notification) => {
      if (!categories.includes(notification.notiType)) {
        categories.push(notification.notiType); // 중복되지 않도록 추가
      }
  });
  return categories;
});

const selectNotiType = (category) => {
  notificationType.value = category;
};

</script>

<template>
  <div @click="handleClickOutside" style="position: relative">
    <!-- 알림 버튼 -->
      <span @click.stop="toggleNotifications" class="notification-button">
      <img src="../assets/img/alram.png" class="button-img"/>
      <span v-show="unreadCount > 0" class="red-dot">{{ unreadCount }}</span>
    </span>

    <!-- 알림 모달 -->
    <Transition name="slide-fade">
    <div v-if="showNotifications" class="modal" id="noti-modal">
      <div class="modal-header">
        <span style="color: black; padding-left: 10px; padding-top: 10px; font-size:x-large; font: bolder;">알림
          
        </span>
        <button @click="deleteAllNotifications" class="delete-all-button">
          모두 삭제
          </button>
      </div>

      <!-- 메시지 필터링 -->
      <div class="category-body">
      
        <span v-for="(categoriy) in notiCatregorys">
          <button @click="selectNotiType(categoriy)" class="category-button">{{ categoriy }}</button>
        </span>
      </div>

      <div class="modal-content">
        <!-- 스크롤 가능한 알림 리스트 -->
        <div class="notification-list">
          <div
            v-for="(notification, index) in filterNotifications"
            :key="notification.notificationId"
            class="notification-item"
            :class="{ 'read': notification.notiReadOrNot}" @click="changeReadStatus(index, notification.notificationId)">
            <div style="flex-direction: row;">
            <span  class="notiMessage-title">{{ notification.notiTitle }}</span>
            <span class="notification-date">· {{ formatTime(notification.createdAt)}}</span>
            <button @click.stop="openDeleteModal(index, notification.notificationId)" class="notification-delete">
              <img src="../assets/img/delete.svg" class="deleteImg">
            </button>
            </div>
            <span  class="notiMessage">{{ notification.notiContent }}</span>
            
            {{ notification.notiReadOrNot }}   
            {{ notification.notiType }}
    
          </div>
        </div>
      </div>

      <!-- 삭제 확인 모달 -->
      <div v-if="showDeleteNotificationModal" class="delete-modal-overlay" @click.stop="closeDeleteModal">
        <div class="delete-modal-target-content-container">
        <div class="delete-modal-targetContent">
            <p>
              <span  class="notiMessage">{{ notifications[deleteIndex].notiTitle }}</span>
              <span class="notification-date">{{ formatTime(notifications[deleteIndex].createdAt)}}</span>
            </p>
            <span  class="notiMessage">{{ notifications[deleteIndex].notiContent }}</span>
          
        </div>
      </div>    
        <div class="delete-modal" >
              <button @click.stop="deleteNotification" class="confirm-delete-button">
                <img src="../assets/img/delete.svg" class="deleteImg">
                삭제하기
              </button>
              <button @click.stop="closeDeleteModal" class="cancel-delete-button">취소</button>
          </div>
        </div>
    </div>
  </Transition>

</div>
</template>

<style scoped>
.inputform{
  position: fixed;
  top: 550px;
}

/* 알림창 버튼 */
.notification-button {
  /* position: absolute; */
  /* right: 10px; */
  /* align-content: center; */
  background : none;
  cursor: pointer;

}

.button-img {
  position: relative;
  border-radius: 50%;
  background: none;
  outline: none;
  width: 50px;
}


.red-dot {
  bottom: 30px;
  right: 0px;
  width: 17px;
  height: 17px;
  background: red;
  border-radius: 50%;
  font-size: 70%;
  text-align: center;
  position: absolute;
  float: right;
  color: white;
}

.modal {
  position: absolute;
  top: 4rem;
  left: -263px;
  width: 323px;
  height: 503px;
  background: rgb(230, 230, 230);
  box-shadow: 5px 5px 5px rgba(0,0,0,0.5);
  z-index: 1000;
  /* overflow-y: auto; */
 
  /* outline-style: solid;
  outline-width: medium; */
  /* outline-color: fuchsia; */
  
  border-radius: 10px;
  display:grid;
  flex-direction: column;
}

.modal-header {
background: white;
height: 50px;
z-index: 1000;
border-top-left-radius: 10px;
border-top-right-radius: 10px;
flex-direction: row;

}


.delete-all-button {
  float:right;
  background: red;
  color: white;
  border: none;
  padding: 5px;
  border-radius: 10px;
  margin-top: 0px;
  margin-right: 5px;
  align-items: center;

  cursor: pointer;
}

.category-body{
  background: rgb(230, 230, 230);
  overflow-x: auto;
  display: flex;
  flex-wrap: wrap;
}

.category-button{
  display: inline-block; /* 버튼 내부 텍스트를 가로로 */
  font-size: 15px;
  border-radius: 20px;
  border: 1px solid black;
  margin: 3px;
  padding: 3px 5px;
  background: white;
  
}

.modal-content {
  padding: 10px;
  height: 340px;
  width: 98%;
  border-radius: 10px;
  overflow-y: auto;
  background-color: transparent;
  outline: none;
  border: none;
}

.notification-list {
  margin-top: 20px;
  
}

.notification-item {
  color: black;
  padding: 10px;
  border-radius: 10px;
  background-color: white;
  margin-bottom: 10px;
  box-shadow: 1px 1px 5px rgba(0,0,0,0.5);

}

.notification-item:hover {
  background: rgb(230, 230, 230);
}

.notification-item.read {
  color: gray;
}
.notiMessage-title{
  background-color: transparent;
}

.notiMessage {
  background-color: transparent ;
}

.notification-date {
  margin-left: 5px;
  font-size: 80%;
  text-align: center;
  display: inline-block;
  justify-content: center;
  background-color: transparent;
}


.notification-delete {
  float:right;
  background: none;
  cursor: pointer;
  border-radius: 25%;
  flex-direction: column;
}

.notification-delete:hover {
  background: rgb(190, 190, 190);
}

.deleteImg {
  width: 20px;
  height: 20px;
}

/* 삭제 확인 모달 스타일 */
.delete-modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(85, 85, 85, 0.5);
  border-radius: 10px;
  z-index: 1100;

  display: flex;
  flex-direction: column;
  align-content: center;
}
.delete-modal-target-content-container{
  flex-grow: 1;
  align-content: center;
}

.delete-modal-targetContent {
  border-radius: 20px;
  background-color: white;
  padding: 10px;
  height: 100px;

  display: flex;
  flex-direction: column;
}

.delete-modal {
  background: none;
  padding: 20px;
  z-index: 1200;
  width: 100%;

  display: flex;
  flex-direction: column;
}


.confirm-delete-button {
  background: white;
  padding: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-bottom: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.cancel-delete-button {
  background: white;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
  height: 30px;
  font-size: 90%;
  display: flex;
  justify-content: center;
  align-items: center;
}


/* 스크롤바 스타일 */
.modal::-webkit-scrollbar {
  width: 10px; /* 스크롤바 너비 */
}

.modal::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.5); /* 스크롤바 색상 */
  border-radius: 10px; /* 스크롤바 모서리 둥글게 */
}

.modal::-webkit-scrollbar-track {
  background-color: rgba(0, 0, 0, 0.1); /* 스크롤바 트랙 색상 */
}


/*
  진입/진출 애니메이션은 다른 지속 시간과
  타이밍 함수를 사용할 수 있습니다.
*/
.slide-fade {
  top: 20%;
    left: 50%;
}

.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  /* transform: translateX(20px); */
  transform: translate(20px, -10px);
  opacity: 0;
}
</style>
