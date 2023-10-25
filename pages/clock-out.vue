<template>
   <div class="card text-black flex items-center justify-center h-[30rem] w-[60rem]">
      <div class="flex flex-row self-end">
         <p class="mr-4 ">Status:</p>
         <div class="bg-clock_in_green rounded-full w-2.5 h-2.5 mx-1 mt-2"></div>
         <p>In</p>
      </div>
      <h1 class="card-title text-2xl mt-24">Good Morning,</h1>
      <p>{{ data[0].first_name }}</p>
      <p class="mt-5 mb-1.5">9:00 AM</p>
      <div class="card-actions">
         <button @click="timeOut" class="btn btn-circle btn-ghost w-36 h-36 mt-1 bg-clock_out_red text-white"> Time Out</button>
      </div>
      <div class="card-actions self-end mt-24">
      <button @click="signOut" class="font-bold btn btn-sm btn-ghost btn-circle w-32 ">Logout<img class="mx-2 w-4 h-4" src="~/assets/icons/exit.png"></button>
      </div>
   </div>
</template>


<script setup lang="ts">

const user = useSupabaseUser();
const supabase = useSupabaseClient();
const userId = user.value?.id;
const currentTime = "9:00" // to be fixed

const signOut = async () => {
 const { error } = await supabase.auth.signOut()

 try {
   await $fetch("/api/_supabase/session", {
     method:"POST",
     body: { event: "SIGNED_OUT", session: null },
   });
 user.value = null;
 navigateTo('/login')
 } catch (error) {
   console.log(error) 
 }
};

const { data } = await supabase
  .from('Employees')
  .select('id, first_name')
  .eq('id', userId )

  const timeOut = async () => {
  console.log("in here");
  console.log(data);
  
  try {
  const { data, error } = await supabase
    .from('Time')
    .insert([
      { id: userId,
        status: 'Out' },
    ])
    .select();
  
  navigateTo('/');
  console.log("ok naman");
  console.log(error);
  
  console.log(data);
  
} catch (error) {
  // Handle the error here, for example, log it or show a user-friendly message
  console.log('An error occurred:', error);
}
}

</script>
