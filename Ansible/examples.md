- name: Example Simple Playbook
  hosts: all
  become: yes

  tasks:
  - name: Copy file example_file to /tmp with permissions
    ansible.builtin.copy:
      src: ./example_file
      dest: /tmp/example_file
      mode: '0644'

  - name: Add the user 'bob' with a specific uid 
    ansible.builtin.user:
      name: bob
      state: present
      uid: 1040

- name: Update postgres servers
  hosts: databases
  become: yes

  tasks:
  - name: Ensure postgres DB is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest

  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started