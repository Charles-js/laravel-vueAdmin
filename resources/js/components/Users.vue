<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <div class="card mt-5">
          <div class="card-header">
            <h3 class="card-title">Users</h3>
            <div class="card-tools">
              <button type="button" class="btn btn-primary" @click="addUserModal">
                Add User
                <i class="fas fa-user-plus fa-fw"></i>
              </button>
            </div>
          </div>
          <div class="card-body">
            <table class="table table-hover">
              <thead>
                <tr>
                  <th scope="col">ID</th>
                  <th scope="col">Name</th>
                  <th scope="col">Email</th>
                  <th scope="col">Type</th>
                  <th scope="col">Created At</th>
                  <th scope="col">Modify</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="user in users" :key="user.id">
                  <th scope="row">{{user.id}}</th>
                  <td>{{user.name}}</td>
                  <td>{{user.email}}</td>
                  <td>{{user.type | upperCase}}</td>
                  <td>{{user.created_at}}</td>
                  <td>
                    <a href="#" @click="editUserModal(user)"><i class="fa fa-edit green"></i> Edit</a> ||
                    <a href="#" @click="deleteUser(user.id)">
                      <i class="fa fa-trash red"></i> Delete
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <!-- Modal -->
        <div class="modal fade" id="adduser" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">Add New</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
              <form @submit.prevent="createUser">
                <div class="modal-body">
                  <div class="form-group">
                    <label>Name</label>
                    <input v-model="form.name" type="text" name="name" placeholder="Queen Latifah"
                      class="form-control" :class="{ 'is-invalid': form.errors.has('username') }">
                      <has-error :form="form" field="name"></has-error>
                  </div>
                  <div class="form-group">
                    <label>Email Address</label>
                    <input v-model="form.email" type="email" name="email" placeholder="you@mail.com"
                      class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                      <has-error :form="form" field="email"></has-error>
                  </div>
                  <div class="form-group">
                    <label>Your Bio</label>
                    <textarea v-model="form.bio" type="text" name="bio" id="bio"
                      placeholder="....Lived in Houston for a while before.."
                      class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                      <has-error :form="form" field="bio"></has-error>
                  </div>
                  <div class="form-group">
                    <select v-model="form.type" class="form-control" name="type" id="type" 
                      :class="{ 'is-invalid': form.errors.has('type') }" >
                      <option value="">Select User Role</option>
                      <option value="admin">Admin</option>
                      <option value="user">Standard User</option>
                      <option value="editor">Editor</option>
                    </select>
                    <has-error :form="form" field="type"></has-error>
                  </div>
                  <div class="form-group">
                    <label>Password</label>
                    <input v-model="form.password" type="password" name="email" placeholder="*******"
                      class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                      <has-error :form="form" field="password"></has-error>
                  </div>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
                  <button type="submit" class="btn btn-primary">Create User</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>      
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: {},
      form: new Form ({
        name: '',
        email: '',
        password: '',
        type: '',
        bio: '',
        photo: ''
      })
    }
  }, 
  methods: {
    addUserModal() {
      this.form.reset()
      $('#adduser').modal('show')
    },
    editUserModal(user) {
      this.form.reset()
      $('#adduser').modal('show')
      this.form.fill(user)
    },
    createUser() {
      this.$Progress.start()
      this.form.post('api/user').then( () => {
        UpdateEvent.$emit('UpdateData');
        this.$Progress.finish()
        $('#adduser').modal('hide')
        toast({
          type: 'success',
          title: 'User Created Successfully'
        })
      }).catch(this.$Progress.fail())
    },
    loadUsers() {
      axios.get("api/user").then(
        ({data}) => (this.users = data.data)
      );
    },
    deleteUser(id) {
      swal({
        title: 'Are you sure?',
        text: "You won't be able to revert this!",
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!'
      }).then((result) => {
        if (result.value) {
          axios.delete("api/user/"+id).then( () => {
          swal(
            'Deleted!',
            'Your file has been deleted.',
            'success'
          )
          UpdateEvent.$emit('UpdateData');
          }).catch()
        }
      })
    }
  },
  // life cycle hook
  created() {
    this.loadUsers();
    UpdateEvent.$on('UpdateData', () => {
      this.loadUsers();
    })
  }
}
</script>
